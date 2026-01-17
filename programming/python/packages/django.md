# Django Quick Reference (Intermediate) — Syntax + Patterns

**Goal**: Core Django-specific methods, patterns, and syntax for day-to-day work.

**Focus**: ORM queryset methods, model relationships, class-based views, forms, admin customization, URL patterns, middleware, signals, management commands, template tags/filters.

## Table of Contents

- [Installation & Setup](#installation--setup)
- [1. Models & ORM](#1-models--orm)
- [2. Queryset Methods](#2-queryset-methods)
- [3. Model Relationships](#3-model-relationships)
- [4. Class-Based Views](#4-class-based-views)
- [5. Forms & Validation](#5-forms--validation)
- [6. URL Patterns](#6-url-patterns)
- [7. Admin Customization](#7-admin-customization)
- [8. Middleware](#8-middleware)
- [9. Signals](#9-signals)
- [10. Template Tags & Filters](#10-template-tags--filters)
- [11. Management Commands](#11-management-commands)
- [12. Testing (Django TestCase)](#12-testing-django-testcase)

---

## Installation & Setup

- **Install**: `pip install django`
- **Create project**: `django-admin startproject myproject && cd myproject && python manage.py startapp myapp`
- **Run**: `python manage.py runserver`, `python manage.py migrate`, `python manage.py createsuperuser`

---

## 1. Models & ORM

```python
from django.db import models
from django.contrib.auth.models import User

class Post(models.Model):
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name="posts")
    title = models.CharField(max_length=200, db_index=True)
    slug = models.SlugField(unique=True)
    content = models.TextField()
    published = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        db_table = "posts"
        ordering = ["-created_at"]
        indexes = [
            models.Index(fields=["-created_at"]),
            models.Index(fields=["published", "-created_at"]),
        ]
        verbose_name = "Post"
        verbose_name_plural = "Posts"
        constraints = [
            models.CheckConstraint(check=models.Q(title__length__gte=5), name="title_min_length"),
        ]
    
    def __str__(self):
        return self.title
    
    def get_absolute_url(self):
        from django.urls import reverse
        return reverse("post_detail", kwargs={"pk": self.pk})
    
    @property
    def is_recent(self):
        from django.utils import timezone
        from datetime import timedelta
        return self.created_at >= timezone.now() - timedelta(days=7)
```

**Field types**: `CharField(max_length=N)`, `TextField()`, `IntegerField()`, `DecimalField(max_digits=10, decimal_places=2)`, `BooleanField()`, `DateField()`, `DateTimeField()`, `EmailField()`, `URLField()`, `SlugField()`, `ForeignKey(Model, on_delete)`, `ManyToManyField(Model)`, `OneToOneField(Model, on_delete)`, `FileField(upload_to="path/")`, `ImageField(upload_to="path/")`

**Field options**: `null=True` (DB), `blank=True` (form), `default=value`, `choices=CHOICES`, `db_index=True`, `unique=True`, `verbose_name="Label"`, `help_text="Help"`, `editable=False`, `auto_now=True` (DateTime), `auto_now_add=True` (DateTime)

**Meta options**: `db_table`, `ordering`, `indexes`, `unique_together`, `constraints`, `verbose_name`, `verbose_name_plural`, `abstract=True`, `managed=False`

---

## 2. Queryset Methods

**Filtering**: `filter(**kwargs)`, `exclude(**kwargs)`, `get(**kwargs)` (raises DoesNotExist), `get_or_create(defaults={})`, `update_or_create(defaults={})`

**Lookups**: `field__exact`, `field__iexact`, `field__contains`, `field__icontains`, `field__startswith`, `field__istartswith`, `field__endswith`, `field__iendswith`, `field__in=[list]`, `field__isnull=True`, `field__gte`, `field__lte`, `field__gt`, `field__lt`, `field__date`, `field__year`, `field__month`, `field__day`, `field__regex`, `field__iregex`

**Chaining**: `filter().exclude().filter()`, `order_by("field")`, `order_by("-field")` (desc), `distinct()`, `reverse()`

**Slicing**: `[0]` (first), `[:10]` (limit), `[10:20]` (offset/limit)

**Aggregation**: `count()`, `exists()`, `aggregate(Count("id"), Avg("field"), Sum("field"), Max("field"), Min("field"))`, `values("field1", "field2").annotate(count=Count("id"))`, `values_list("field", flat=True)`

**Optimization**: `select_related("foreign_key_field")` (JOIN), `prefetch_related("many_to_many_field")` (separate query), `only("field1", "field2")` (defer others), `defer("field")` (exclude), `select_for_update()` (row lock)

**Examples**:
```python
Post.objects.filter(published=True, created_at__gte=timezone.now() - timedelta(days=7))
Post.objects.exclude(author__is_staff=False)
Post.objects.filter(title__icontains="django").order_by("-created_at")[:10]
Post.objects.select_related("author").prefetch_related("tags").all()
Post.objects.aggregate(Count("id"), Avg("id"))
Post.objects.values("author").annotate(post_count=Count("id"))
user.posts.filter(published=True).exists()
```

---

## 3. Model Relationships

**ForeignKey**: `models.ForeignKey(Model, on_delete=models.CASCADE, related_name="posts")` - Access: `post.author`, `user.posts.all()` (via related_name)

**ManyToManyField**: `models.ManyToManyField(Model, related_name="tags")` - Access: `post.tags.all()`, `post.tags.add(tag)`, `post.tags.remove(tag)`, `post.tags.clear()`, `post.tags.set([tag1, tag2])`

**OneToOneField**: `models.OneToOneField(Model, on_delete=models.CASCADE, related_name="profile")` - Access: `user.profile`, `profile.user`

**on_delete options**: `CASCADE` (delete related), `PROTECT` (prevent delete), `SET_NULL` (null=True required), `SET_DEFAULT` (default required), `SET(value)`, `DO_NOTHING`

**Through model** (custom M2M): `models.ManyToManyField(Model, through="ThroughModel")` - Allows extra fields on relationship

**Reverse relationships**: Use `related_name` to access reverse side: `user.posts.all()` if `Post.author` has `related_name="posts"`

---

## 4. Class-Based Views

```python
from django.views.generic import ListView, DetailView, CreateView, UpdateView, DeleteView
from django.contrib.auth.mixins import LoginRequiredMixin, UserPassesTestMixin, PermissionRequiredMixin

class PostListView(ListView):
    model = Post
    template_name = "posts/list.html"
    context_object_name = "posts"
    paginate_by = 20
    def get_queryset(self):
        return Post.objects.filter(published=True).select_related("author")
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context["total"] = Post.objects.count()
        return context

class PostDetailView(DetailView):
    model = Post
    template_name = "posts/detail.html"
    context_object_name = "post"
    def get_object(self, queryset=None):
        obj = super().get_object(queryset)
        obj.view_count += 1
        obj.save(update_fields=["view_count"])
        return obj

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    fields = ["title", "content", "published"]
    template_name = "posts/create.html"
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)
    def get_success_url(self):
        return self.object.get_absolute_url()

class PostUpdateView(LoginRequiredMixin, UserPassesTestMixin, UpdateView):
    model = Post
    fields = ["title", "content", "published"]
    template_name = "posts/update.html"
    def test_func(self):
        return self.get_object().author == self.request.user

class PostDeleteView(LoginRequiredMixin, PermissionRequiredMixin, DeleteView):
    model = Post
    permission_required = "myapp.delete_post"
    success_url = "/posts/"
```

**Generic views**: `ListView`, `DetailView`, `CreateView`, `UpdateView`, `DeleteView`, `FormView`, `TemplateView`, `RedirectView`

**Mixins**: `LoginRequiredMixin`, `UserPassesTestMixin` (override `test_func()`), `PermissionRequiredMixin` (set `permission_required`), `MultipleObjectMixin` (for ListView), `SingleObjectMixin` (for DetailView)

**Methods to override**: `get_queryset()`, `get_object()`, `get_context_data()`, `form_valid()`, `form_invalid()`, `get_success_url()`

---

## 5. Forms & Validation

```python
# myproject/urls.py
from django.contrib import admin
from django.urls import path, include, re_path
from django.views.generic import RedirectView

urlpatterns = [
    path("admin/", admin.site.urls),
    path("api/", include("myapp.urls")),
    path("", include("myapp.urls")),
    re_path(r"^old-url/$", RedirectView.as_view(url="/new-url/", permanent=True)),
]

# myapp/urls.py
from django.urls import path, re_path
from . import views

app_name = "myapp"  # Namespace for reverse()

urlpatterns = [
    path("posts/", views.PostListView.as_view(), name="post_list"),
    path("posts/<int:pk>/", views.PostDetailView.as_view(), name="post_detail"),
    path("posts/<slug:slug>/", views.PostDetailView.as_view(), name="post_detail_slug"),
    path("posts/create/", views.PostCreateView.as_view(), name="post_create"),
    re_path(r"^posts/(?P<year>\d{4})/(?P<month>\d{2})/$", views.PostArchiveView.as_view(), name="post_archive"),
]
```

**Path converters**: `<int:pk>`, `<str:slug>`, `<slug:slug>`, `<uuid:id>`, `<path:path>` (matches full path including slashes)

**Regex patterns**: `re_path(r"^pattern/$", view)` - Use named groups: `(?P<name>pattern)`

**Include**: `include("app.urls")`, `include(("app.urls", "namespace"))` - Namespace for reverse()

**Reverse**: `from django.urls import reverse`; `reverse("app:view_name", kwargs={"pk": 1})`, `reverse("app:view_name", args=[1])`

---

## 7. Admin Customization

```python
from django.contrib import admin
from .models import Post, Tag

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ["title", "author", "published", "created_at", "view_count"]
    list_display_links = ["title"]
    list_editable = ["published"]
    list_filter = ["published", "created_at", "author"]
    search_fields = ["title", "content"]
    readonly_fields = ["created_at", "updated_at", "view_count"]
    fieldsets = (
        ("Content", {"fields": ("title", "content")}),
        ("Metadata", {"fields": ("author", "published", "created_at")}),
    )
    date_hierarchy = "created_at"
    ordering = ["-created_at"]
    list_per_page = 25
    
    def get_queryset(self, request):
        qs = super().get_queryset(request)
        return qs.select_related("author")
    
    def make_published(self, request, queryset):
        queryset.update(published=True)
    make_published.short_description = "Mark selected posts as published"
    actions = [make_published]

admin.site.register(Tag)
```

**Admin options**: `list_display`, `list_display_links`, `list_editable`, `list_filter`, `search_fields`, `readonly_fields`, `fieldsets`, `date_hierarchy`, `ordering`, `list_per_page`, `actions`, `get_queryset()`, `save_model()`, `delete_model()`

**Inline admin**: `class TagInline(admin.TabularInline): model = Tag; fields = ["name"]` - Add to `PostAdmin`: `inlines = [TagInline]`

---

## 8. Middleware

```python
# middleware.py
class TimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        import time
        start = time.time()
        response = self.get_response(request)
        response["X-Process-Time"] = str(time.time() - start)
        return response
    
    def process_view(self, request, view_func, view_args, view_kwargs):
        # Called before view
        return None
    
    def process_exception(self, request, exception):
        # Called if view raises exception
        return None
    
    def process_template_response(self, request, response):
        # Called if response has render() method
        return response

# settings.py
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "django.contrib.messages.middleware.MessageMiddleware",
    "myapp.middleware.TimingMiddleware",
]
```

**Middleware hooks**: `__call__(request)` (required), `process_request(request)`, `process_view(request, view_func, view_args, view_kwargs)`, `process_template_response(request, response)`, `process_exception(request, exception)`, `process_response(request, response)`

---

## 9. Signals

```python
from django.db.models.signals import pre_save, post_save, pre_delete, post_delete, m2m_changed
from django.dispatch import receiver
from .models import Post

@receiver(pre_save, sender=Post)
def pre_save_post(sender, instance, **kwargs):
    if not instance.slug:
        from django.utils.text import slugify
        instance.slug = slugify(instance.title)

@receiver(post_save, sender=Post)
def post_save_post(sender, instance, created, **kwargs):
    if created:
        print(f"New post created: {instance.title}")

@receiver(m2m_changed, sender=Post.tags.through)
def tags_changed(sender, instance, action, **kwargs):
    if action == "post_add":
        print(f"Tags added to {instance.title}")
```

**Built-in signals**: `pre_save`, `post_save`, `pre_delete`, `post_delete`, `m2m_changed`, `pre_init`, `post_init`, `pre_migrate`, `post_migrate`

**Custom signals**: `from django.dispatch import Signal; my_signal = Signal(providing_args=["arg1", "arg2"])`; `my_signal.send(sender=MyModel, arg1=value1)`

---

## 10. Template Tags & Filters

```python
# templatetags/my_tags.py
from django import template
register = template.Library()

@register.filter
def multiply(value, arg):
    return value * arg

@register.filter(name="lower")
def lower_filter(value):
    return value.lower()

@register.simple_tag
def current_time(format_string):
    from django.utils import timezone
    return timezone.now().strftime(format_string)

@register.inclusion_tag("tags/post_list.html")
def show_posts(posts):
    return {"posts": posts}

@register.simple_tag(takes_context=True)
def user_posts(context):
    user = context["user"]
    return Post.objects.filter(author=user)
```

**Usage in templates**: `{% load my_tags %}`; `{{ value|multiply:2 }}`; `{% current_time "%Y-%m-%d" %}`; `{% show_posts posts %}`

**Built-in filters**: `{{ value|length }}`, `{{ value|default:"N/A" }}`, `{{ value|date:"Y-m-d" }}`, `{{ value|truncatewords:10 }}`, `{{ value|upper }}`, `{{ value|lower }}`, `{{ value|slice:":5" }}`

**Built-in tags**: `{% if %}`, `{% for %}`, `{% url "view_name" pk=1 %}`, `{% csrf_token %}`, `{% load %}`, `{% extends %}`, `{% block %}`, `{% include %}`

---

## 11. Management Commands

```python
# management/commands/my_command.py
from django.core.management.base import BaseCommand
from myapp.models import Post

class Command(BaseCommand):
    help = "Description of command"
    
    def add_arguments(self, parser):
        parser.add_argument("--published", action="store_true", help="Only published posts")
        parser.add_argument("--limit", type=int, default=10, help="Limit results")
    
    def handle(self, *args, **options):
        queryset = Post.objects.all()
        if options["published"]:
            queryset = queryset.filter(published=True)
        posts = queryset[:options["limit"]]
        for post in posts:
            self.stdout.write(self.style.SUCCESS(f"Post: {post.title}"))
        self.stdout.write(f"Processed {posts.count()} posts")
```

**Run**: `python manage.py my_command --published --limit=5`

**Built-in commands**: `migrate`, `makemigrations`, `createsuperuser`, `shell`, `runserver`, `collectstatic`, `test`, `dumpdata`, `loaddata`

---

## 12. Testing (Django TestCase)

```python
from django.test import TestCase, Client, TransactionTestCase
from django.contrib.auth.models import User
from django.urls import reverse
from .models import Post

class PostTestCase(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(username="test", password="test")
        self.client = Client()
        self.post = Post.objects.create(author=self.user, title="Test", content="Content")
    
    def test_model_str(self):
        self.assertEqual(str(self.post), "Test")
    
    def test_get_absolute_url(self):
        url = self.post.get_absolute_url()
        self.assertIsNotNone(url)
    
    def test_list_view(self):
        response = self.client.get(reverse("post_list"))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "Test")
    
    def test_create_view(self):
        self.client.login(username="test", password="test")
        response = self.client.post(reverse("post_create"), {
            "title": "New Post",
            "content": "New Content"
        })
        self.assertEqual(response.status_code, 302)
        self.assertTrue(Post.objects.filter(title="New Post").exists())
    
    def test_queryset_filtering(self):
        published = Post.objects.filter(published=True)
        self.assertEqual(published.count(), 0)
```

**Test client methods**: `client.get(url)`, `client.post(url, data)`, `client.login(username, password)`, `client.logout()`, `client.force_login(user)`

**Assertions**: `assertEqual()`, `assertNotEqual()`, `assertTrue()`, `assertFalse()`, `assertIsNone()`, `assertContains(response, text)`, `assertNotContains(response, text)`, `assertTemplateUsed(response, template)`, `assertRedirects(response, expected_url)`

**Fixtures**: `fixtures = ["initial_data.json"]` in TestCase - Loads data from `fixtures/` directory

---

> **Note**: Django ORM provides powerful queryset methods for filtering, aggregation, and optimization. Use `select_related()` for ForeignKey, `prefetch_related()` for ManyToMany. Class-based views offer reusable patterns with mixins. Forms support field-level and form-level validation. Admin is highly customizable with list displays, filters, and actions.

