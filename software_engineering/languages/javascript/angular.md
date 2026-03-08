# Angular Quick Reference

**Modern Angular (14+)** — Standalone components, reactive forms, functional guards, RxJS patterns.

---

## Setup

```bash
npm install -g @angular/cli
ng new my-app
ng g c components/user    # component
ng g s services/user       # service
ng serve
```

**CLI**: `ng g c|s|m|d|p|g|i`, `ng test`, `ng lint`

---

## Components

**Core**: Template + class + metadata. Data down (`@Input`), events up (`@Output`).

```typescript
// Standalone component
@Component({
  selector: 'app-user',
  standalone: true,
  imports: [CommonModule],
  template: `<div>{{ user.name }}</div>`
})
export class UserComponent {
  user = { name: 'John', email: 'john@example.com' };
}

// Input/Output
@Input() user!: { id: number; name: string };
@Output() userSelected = new EventEmitter<number>();
onSelect(): void { this.userSelected.emit(this.user.id); }

// ViewChild
@ViewChild('searchInput') searchInput!: ElementRef<HTMLInputElement>;
ngAfterViewInit(): void { this.searchInput.nativeElement.focus(); }
```

**Options**: `selector`, `template` / `templateUrl`, `styles` / `styleUrls`, `standalone`, `imports`, `providers`, `changeDetection`

---

## Services & DI

**Core**: Services = business logic. DI provides singletons (`providedIn: 'root'`). Inject via constructor.

```typescript
@Injectable({ providedIn: 'root' })
export class UserService {
  private users: User[] = [];
  getUsers(): User[] { return this.users; }
}

// Component injection
constructor(private userService: UserService) {}
ngOnInit(): void { this.users = this.userService.getUsers(); }
```

**Scope**: `providedIn: 'root'` (singleton), `providedIn: 'platform'`, `providedIn: 'any'`

---

## Routing

**Core**: Router maps URLs to components. Functional guards protect routes.

```typescript
// Routes
export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'users/:id', component: UserDetailComponent },
  { path: 'admin', component: AdminComponent, canActivate: [authGuard] },
  { path: '**', redirectTo: '' }
];

// Bootstrap
bootstrapApplication(AppComponent, { providers: [provideRouter(routes)] });

// Route params
ngOnInit(): void { this.userId = this.route.snapshot.paramMap.get('id')!; }
navigate(): void { this.router.navigate(['/users']); }

// Guard
export const authGuard: CanActivateFn = (route, state) => {
  return inject(AuthService).isAuthenticated() 
    ? true 
    : inject(Router).navigate(['/login']);
};
```

**Methods**: `router.navigate(['path'])`, `router.navigateByUrl('path')`, `router.navigate(['path'], { queryParams: {...} })`

**Access**: `route.snapshot.paramMap.get('id')`, `route.queryParamMap.get('page')`, `route.data`

---

## Reactive Forms

**Core**: Model-driven forms. Preferred for complex forms and testing.

```typescript
// Form setup
userForm = this.fb.group({
  name: ['', [Validators.required, Validators.minLength(3)]],
  email: ['', [Validators.required, Validators.email]]
});

// Template
<form [formGroup]="userForm" (ngSubmit)="onSubmit()">
  <input formControlName="name">
  <div *ngIf="name.invalid && name.touched">
    <div *ngIf="name.errors?.['required']">Name required</div>
  </div>
  <button type="submit" [disabled]="userForm.invalid">Submit</button>
</form>

// Nested groups
this.userForm = this.fb.group({
  personalInfo: this.fb.group({
    firstName: ['', Validators.required],
    lastName: ['', Validators.required]
  })
});
// Template: <form [formGroup]="userForm"><div formGroupName="personalInfo">...</div></form>

// Custom validator
export function passwordMatchValidator(): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const pwd = control.get('password');
    const confirm = control.get('confirmPassword');
    return pwd?.value === confirm?.value ? null : { passwordMismatch: true };
  };
}
// Usage: this.fb.group({...}, { validators: passwordMatchValidator() })
```

**FormBuilder**: `group()`, `control()`, `array()`

**FormControl**: `setValue()`, `patchValue()`, `reset()`, `disable()`, `enable()`, `markAsTouched()`

**Validators**: `required`, `min()`, `max()`, `minLength()`, `maxLength()`, `pattern()`, `email()`

---

## HTTP Client

**Core**: Returns Observables. Use interceptors for global handling. Always handle errors.

```typescript
// Service
@Injectable({ providedIn: 'root' })
export class ApiService {
  constructor(private http: HttpClient) {}
  getUsers(): Observable<User[]> { return this.http.get<User[]>(`${this.apiUrl}/users`); }
  createUser(user: User): Observable<User> { return this.http.post<User>(`${this.apiUrl}/users`, user); }
  updateUser(id: number, user: Partial<User>): Observable<User> { return this.http.put<User>(`${this.apiUrl}/users/${id}`, user); }
  deleteUser(id: number): Observable<void> { return this.http.delete<void>(`${this.apiUrl}/users/${id}`); }
}

// Query params
const params = new HttpParams().set('page', page.toString()).set('limit', limit.toString());
return this.http.get<User[]>(`${this.apiUrl}/users`, { params });

// Headers
const headers = new HttpHeaders().set('Authorization', `Bearer ${this.token}`);
return this.http.get<Data>(`${this.apiUrl}/protected`, { headers });

// Error handling
return this.http.get<User[]>(`${this.apiUrl}/users`).pipe(
  catchError(error => { console.error(error); return throwError(() => new Error('Failed')); })
);

// Interceptor
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(AuthService).getToken();
  if (token) { req = req.clone({ setHeaders: { Authorization: `Bearer ${token}` } }); }
  return next(req);
};
// Register: provideHttpClient(withInterceptors([authInterceptor]))
```

**Methods**: `get<T>()`, `post<T>()`, `put<T>()`, `patch<T>()`, `delete<T>()`

**RxJS**: `catchError()`, `retry()`, `timeout()`, `map()`, `switchMap()`, `mergeMap()`, `tap()`

---

## Directives

**Core**: Structural (`*ngIf`, `*ngFor`) modify DOM. Attribute modify appearance/behavior.

```typescript
// Built-in
<div *ngIf="isVisible">Content</div>
<div *ngFor="let item of items; let i = index; trackBy: trackById">{{ item.name }}</div>
<div [ngSwitch]="status">
  <div *ngSwitchCase="'active'">Active</div>
  <div *ngSwitchDefault>Unknown</div>
</div>
<div [ngClass]="{'active': isActive}" [ngStyle]="{'color': textColor}">Styled</div>

// Custom attribute
@Directive({ selector: '[appHighlight]', standalone: true })
export class HighlightDirective implements OnInit {
  @Input() appHighlight = 'yellow';
  constructor(private el: ElementRef, private renderer: Renderer2) {}
  ngOnInit(): void {
    this.renderer.setStyle(this.el.nativeElement, 'background-color', this.appHighlight);
  }
}
// Usage: <div appHighlight="lightblue">Highlighted</div>

// Custom structural
@Directive({ selector: '[appUnless]', standalone: true })
export class UnlessDirective {
  constructor(private templateRef: TemplateRef<any>, private viewContainer: ViewContainerRef) {}
  @Input() set appUnless(condition: boolean) {
    if (!condition) this.viewContainer.createEmbeddedView(this.templateRef);
    else this.viewContainer.clear();
  }
}
// Usage: <div *appUnless="isHidden">Content</div>
```

**Built-in**: `*ngIf`, `*ngFor`, `[ngSwitch]`, `[ngClass]`, `[ngStyle]`, `[ngModel]`

---

## Pipes

**Core**: Transform data for display. Pure (default) runs when input changes. Impure runs every change detection.

```typescript
// Built-in
{{ user.name | uppercase }}
{{ price | currency:'USD':'symbol':'1.2-2' }}
{{ date | date:'short' }}
{{ date | date:'yyyy-MM-dd' }}
{{ items | slice:0:5 }}
{{ user | json }}
{{ value | number:'1.2-2' }}
{{ text | async }} // Observables/Promises

// Custom
@Pipe({ name: 'truncate', standalone: true })
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 20, trail: string = '...'): string {
    return !value ? '' : value.length > limit ? value.substring(0, limit) + trail : value;
  }
}
// Usage: {{ longText | truncate:50:'...' }}
```

**Built-in**: `uppercase`, `lowercase`, `titlecase`, `currency`, `date`, `number`, `percent`, `json`, `slice`, `async`, `i18nSelect`, `i18nPlural`, `keyvalue`

**Pure**: `pure: true` (default), `pure: false` (runs every change detection)

---

## Lifecycle Hooks

**Core**: Most common: `ngOnInit` (init), `ngOnDestroy` (cleanup).

```typescript
ngOnChanges(changes: SimpleChanges): void { } // After @Input() changes
ngOnInit(): void { } // Once after first ngOnChanges
ngDoCheck(): void { } // Every change detection
ngAfterContentInit(): void { } // After content projection
ngAfterViewInit(): void { } // After view/child views initialized
ngOnDestroy(): void { /* Cleanup: unsubscribe, clear timers */ } // Before destruction
```

**Order**: `ngOnChanges` → `ngOnInit` → `ngDoCheck` → `ngAfterContentInit` → `ngAfterContentChecked` → `ngAfterViewInit` → `ngAfterViewChecked` → `ngOnDestroy`

---

## Bootstrap

```typescript
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes),
    provideHttpClient(),
    provideHttpClient(withInterceptors([authInterceptor]))
  ]
});
```

---

## Testing

**Core**: `TestBed` configures testing. `HttpTestingController` mocks HTTP.

```typescript
// Component
beforeEach(async () => {
  await TestBed.configureTestingModule({ imports: [UserComponent] }).compileComponents();
  fixture = TestBed.createComponent(UserComponent);
  component = fixture.componentInstance;
  fixture.detectChanges();
});
it('should create', () => { expect(component).toBeTruthy(); });

// Service
beforeEach(() => {
  TestBed.configureTestingModule({
    imports: [HttpClientTestingModule],
    providers: [ApiService]
  });
  service = TestBed.inject(ApiService);
  httpMock = TestBed.inject(HttpTestingController);
});
it('should fetch users', () => {
  service.getUsers().subscribe(users => { expect(users).toEqual(mockUsers); });
  const req = httpMock.expectOne('/api/users');
  expect(req.request.method).toBe('GET');
  req.flush(mockUsers);
});
```

**Jasmine**: `toBe()`, `toEqual()`, `toBeTruthy()`, `toContain()`, `toBeDefined()`, `toBeNull()`

**Angular**: `TestBed.configureTestingModule()`, `fixture.detectChanges()`, `fixture.nativeElement`, `HttpTestingController`

---

## Common Patterns

### 1. Data Sharing with Service

```typescript
@Injectable({ providedIn: 'root' })
export class DataService {
  private dataSubject = new BehaviorSubject<any>(null);
  data$ = this.dataSubject.asObservable();
  setData(data: any): void { this.dataSubject.next(data); }
}
// Component A: sendData() { this.dataService.setData({ message: 'Hello' }); }
// Component B: data$ = this.dataService.data$;
```

### 2. Loading State

```typescript
users$ = this.userService.getUsers().pipe(
  startWith([]),
  catchError(error => { this.error = error.message; return of([]); })
);
loading$ = this.users$.pipe(map(() => false), startWith(true));
```

### 3. Async Form Validation

```typescript
form = this.fb.group({
  email: ['', [Validators.required, Validators.email], [this.emailValidator.validate.bind(this.emailValidator)]]
});
@Injectable({ providedIn: 'root' })
export class EmailValidator {
  validate(control: AbstractControl): Observable<ValidationErrors | null> {
    return this.http.get(`/api/check-email?email=${control.value}`).pipe(
      map(exists => exists ? { emailTaken: true } : null),
      catchError(() => of(null))
    );
  }
}
```

### 4. Route Resolver

```typescript
export const userResolver: ResolveFn<User> = (route, state) => {
  return inject(UserService).getUserById(+route.paramMap.get('id')!);
};
// Routes: { path: 'users/:id', component: UserDetailComponent, resolve: { user: userResolver } }
// Component: this.user = this.route.snapshot.data['user'];
```

### 5. Error Interceptor

```typescript
export const errorInterceptor: HttpInterceptorFn = (req, next) => {
  return next(req).pipe(
    catchError(error => {
      if (error.status === 401) inject(Router).navigate(['/login']);
      else if (error.status >= 500) console.error('Server error:', error);
      return throwError(() => error);
    })
  );
};
```

### 6. Debounced Search

```typescript
private searchTerms = new Subject<string>();
results$ = this.searchTerms.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(term => this.searchService.search(term))
);
search(term: string): void { this.searchTerms.next(term); }
```

---

## Best Practices

- **Standalone components** (Angular 14+) for all new projects
- **OnPush change detection**: `changeDetection: ChangeDetectionStrategy.OnPush`
- **RxJS operators**: `map()`, `switchMap()`, `catchError()`, `debounceTime()`, `distinctUntilChanged()`, `takeUntil()`
- **TypeScript strict mode** for type safety
- **trackBy** in `*ngFor`: `*ngFor="let item of items; trackBy: trackById"`
- **Lazy loading** routes: `loadComponent: () => import('./lazy.component').then(m => m.LazyComponent)`
- **Reactive forms** over template-driven forms
- **Functional guards** (Angular 15+) over class-based guards
- **Unsubscribe** in `ngOnDestroy` or use `takeUntil()` operator
- **Signal-based reactivity** (Angular 16+) for modern state management
