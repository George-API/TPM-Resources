# Software Development Life Cycle (SDLC)

**Scope**: Software development life cycle phases, methodologies, and best practices for planning, building, and maintaining software systems.

**Purpose**: Use this when planning software projects, choosing development methodologies, and understanding the software development process from conception to retirement.

## Table of Contents

- [1. SDLC Phases](#1-sdlc-phases)
- [2. Development Methodologies](#2-development-methodologies)
- [3. Requirements Management](#3-requirements-management)
- [4. Design & Architecture](#4-design--architecture)
- [5. Development Practices](#5-development-practices)
- [6. Testing Strategies](#6-testing-strategies)
- [7. Deployment & Release Management](#7-deployment--release-management)
- [8. Maintenance & Evolution](#8-maintenance--evolution)
- [9. Project Management](#9-project-management)
- [10. Quality Assurance](#10-quality-assurance)

---

## 1. SDLC Phases

### Planning Phase

- **Requirements gathering**: Understand business needs, user stories, functional/non-functional requirements
- **Feasibility study**: Technical, economic, operational feasibility
- **Resource planning**: Team, budget, timeline, tools
- **Risk assessment**: Identify and plan for risks
- **Project charter**: Define scope, objectives, stakeholders

### Analysis Phase

- **Requirements analysis**: Detailed requirements specification
- **System analysis**: Current system assessment, gap analysis
- **Stakeholder analysis**: Identify and engage stakeholders
- **Use case modeling**: User interactions and system behavior
- **Requirements documentation**: SRS (Software Requirements Specification)

### Design Phase

- **System design**: High-level architecture, components, interfaces
- **Database design**: Data models, schemas, relationships
- **UI/UX design**: User interface, user experience, wireframes
- **Technical design**: Detailed technical specifications
- **Security design**: Security architecture, threat modeling

### Implementation Phase

- **Coding**: Write code following standards and best practices
- **Code reviews**: Peer reviews, quality checks
- **Unit testing**: Test individual components
- **Integration**: Integrate components and modules
- **Version control**: Manage code changes and versions

### Testing Phase

- **Test planning**: Test strategy, test cases, test data
- **Unit testing**: Test individual components
- **Integration testing**: Test component interactions
- **System testing**: Test complete system
- **User acceptance testing**: Validate with end users
- **Performance testing**: Load, stress, scalability testing
- **Security testing**: Vulnerability assessment, penetration testing

### Deployment Phase

- **Deployment planning**: Rollout strategy, environment setup
- **Production deployment**: Deploy to production environment
- **Data migration**: Migrate existing data if applicable
- **User training**: Train end users on new system
- **Go-live support**: Monitor and support initial deployment

### Maintenance Phase

- **Bug fixes**: Fix defects and issues
- **Enhancements**: Add new features, improve functionality
- **Performance optimization**: Improve system performance
- **Security updates**: Apply security patches and updates
- **Documentation updates**: Keep documentation current

---

## 2. Development Methodologies

### Waterfall

- **Sequential phases**: Requirements → Design → Implementation → Testing → Deployment
- **Documentation-heavy**: Extensive documentation at each phase
- **Fixed scope**: Requirements locked early
- **Use cases**: Well-defined requirements, stable technology, regulatory compliance
- **Pros**: Clear milestones, predictable timeline, comprehensive documentation
- **Cons**: Inflexible, late feedback, high risk

### Agile

- **Iterative and incremental**: Short iterations (sprints), working software frequently
- **Collaborative**: Cross-functional teams, customer collaboration
- **Adaptive**: Respond to change, embrace uncertainty
- **Values**: Individuals and interactions, working software, customer collaboration, responding to change
- **Pros**: Flexibility, early feedback, customer satisfaction
- **Cons**: Less predictable, requires discipline, documentation may suffer

### Scrum

- **Framework**: Roles (Product Owner, Scrum Master, Development Team), events (Sprint, Daily Scrum, Sprint Review, Retrospective), artifacts (Product Backlog, Sprint Backlog, Increment)
- **Sprints**: Time-boxed iterations (typically 2-4 weeks)
- **Ceremonies**: Sprint planning, daily standup, sprint review, retrospective
- **Artifacts**: Product backlog, sprint backlog, increment

### Kanban

- **Visual workflow**: Board with columns (To Do, In Progress, Done)
- **Work in progress (WIP) limits**: Limit items in progress
- **Continuous flow**: Pull work when capacity available
- **No iterations**: Continuous delivery, no fixed sprints
- **Use cases**: Support/maintenance work, continuous delivery, operations

### DevOps

- **Integration**: Development and operations collaboration
- **Automation**: CI/CD pipelines, automated testing, infrastructure as code
- **Continuous delivery**: Frequent, reliable releases
- **Monitoring**: Real-time monitoring and feedback
- **Culture**: Shared responsibility, blameless postmortems

### Lean

- **Eliminate waste**: Remove non-value-adding activities
- **Amplify learning**: Build-measure-learn cycles
- **Decide as late as possible**: Defer irreversible decisions
- **Deliver as fast as possible**: Reduce cycle time
- **Empower team**: Give team authority and responsibility

---

## 3. Requirements Management

### Requirements Types

- **Functional requirements**: What the system should do
- **Non-functional requirements**: Performance, security, usability, reliability
- **Business requirements**: Business objectives, success criteria
- **User requirements**: User needs, user stories
- **System requirements**: Technical constraints, interfaces

### Requirements Gathering Techniques

- **Interviews**: One-on-one or group interviews with stakeholders
- **Surveys**: Questionnaires for large user groups
- **Workshops**: Collaborative sessions with stakeholders
- **Observation**: Observe users in their environment
- **Prototyping**: Build prototypes to gather feedback
- **Document analysis**: Review existing documentation

### Requirements Documentation

- **User stories**: As a [user], I want [feature], so that [benefit]
- **Use cases**: Actor, goal, preconditions, main flow, alternative flows
- **Requirements specification**: Detailed functional and non-functional requirements
- **Acceptance criteria**: Conditions that must be met for requirement to be complete
- **Requirements traceability**: Link requirements to design, code, tests

### Requirements Management

- **Prioritization**: MoSCoW (Must have, Should have, Could have, Won't have)
- **Change management**: Process for handling requirement changes
- **Requirements validation**: Ensure requirements are correct and complete
- **Requirements verification**: Ensure system meets requirements

---

## 4. Design & Architecture

### Design Principles

- **Separation of concerns**: Separate different aspects of system
- **Modularity**: Divide system into independent modules
- **Abstraction**: Hide implementation details
- **Encapsulation**: Bundle data and methods together
- **Inheritance**: Reuse code through inheritance
- **Polymorphism**: Same interface, different implementations

### Architecture Patterns

- **Layered architecture**: Presentation, business, data layers
- **Microservices**: Small, independent services
- **Event-driven**: Services communicate via events
- **Service-oriented (SOA)**: Services with well-defined interfaces
- **Monolithic**: Single deployable unit

### Design Documentation

- **Architecture diagrams**: High-level system structure
- **Component diagrams**: Components and their relationships
- **Sequence diagrams**: Interaction flows
- **Database schemas**: Data models and relationships
- **API specifications**: Interface contracts

---

## 5. Development Practices

### Coding Standards

- **Style guides**: Consistent code formatting and naming
- **Code reviews**: Peer review for quality and knowledge sharing
- **Pair programming**: Two developers work together
- **Refactoring**: Improve code structure without changing behavior
- **Documentation**: Code comments, README files, API docs

### Version Control

- **Git workflows**: Feature branches, pull requests, code review
- **Branching strategies**: Git Flow, GitHub Flow, trunk-based
- **Commit messages**: Clear, descriptive commit messages
- **Tagging**: Version tags for releases

### Development Tools

- **IDEs**: Integrated development environments
- **Linters**: Code quality and style checking
- **Formatters**: Automatic code formatting
- **Debuggers**: Debug and troubleshoot code
- **Package managers**: Dependency management

---

## 6. Testing Strategies

### Testing Levels

- **Unit testing**: Test individual components/functions
- **Integration testing**: Test component interactions
- **System testing**: Test complete system
- **Acceptance testing**: Validate with end users
- **Regression testing**: Ensure changes don't break existing functionality

### Testing Types

- **Functional testing**: Test functionality
- **Performance testing**: Load, stress, scalability
- **Security testing**: Vulnerability assessment, penetration testing
- **Usability testing**: User experience testing
- **Compatibility testing**: Test across platforms, browsers, devices

### Test Automation

- **Unit test frameworks**: JUnit, pytest, Mocha
- **Integration test tools**: Test containers, mock services
- **E2E testing**: Selenium, Cypress, Playwright
- **CI/CD integration**: Automated testing in pipelines
- **Test coverage**: Measure code coverage

---

## 7. Deployment & Release Management

### Deployment Strategies

- **Big bang**: Deploy entire system at once
- **Phased rollout**: Deploy to subset of users, gradually expand
- **Blue-green**: Two identical environments, switch traffic
- **Canary**: Deploy to small subset, monitor, then expand
- **Rolling**: Deploy incrementally, one instance at a time

### Release Management

- **Versioning**: Semantic versioning (major.minor.patch)
- **Release planning**: Plan releases, coordinate deployments
- **Change management**: Process for managing changes
- **Rollback procedures**: Plan for reverting deployments
- **Release notes**: Document changes and new features

### Environment Management

- **Development**: Local development environments
- **Testing**: Test environments for QA
- **Staging**: Production-like environment for final testing
- **Production**: Live environment for end users
- **Environment parity**: Keep environments similar

---

## 8. Maintenance & Evolution

### Maintenance Types

- **Corrective**: Fix bugs and defects
- **Adaptive**: Adapt to changing environment (OS, platforms)
- **Perfective**: Improve performance, usability, maintainability
- **Preventive**: Refactor, update documentation, improve code quality

### Evolution Strategies

- **Feature additions**: Add new functionality
- **Performance optimization**: Improve speed and efficiency
- **Refactoring**: Improve code structure
- **Technology updates**: Upgrade frameworks, libraries, platforms
- **Architecture evolution**: Evolve system architecture

### Technical Debt

- **Definition**: Shortcuts taken that need to be addressed later
- **Types**: Code debt, design debt, test debt, documentation debt
- **Management**: Track, prioritize, plan repayment
- **Prevention**: Code reviews, automated checks, standards

---

## 9. Project Management

### Planning

- **Scope definition**: Define project boundaries
- **Work breakdown structure**: Break work into manageable tasks
- **Estimation**: Estimate effort, time, resources
- **Scheduling**: Create project timeline
- **Resource allocation**: Assign team members to tasks

### Tracking

- **Progress tracking**: Monitor task completion
- **Burndown charts**: Track work remaining
- **Velocity**: Measure team productivity
- **Risk management**: Identify and mitigate risks
- **Issue tracking**: Track and resolve issues

### Communication

- **Stakeholder communication**: Regular updates to stakeholders
- **Team communication**: Daily standups, status updates
- **Documentation**: Project documentation, status reports
- **Meetings**: Sprint planning, reviews, retrospectives

---

## 10. Quality Assurance

### Quality Metrics

- **Code quality**: Complexity, maintainability, test coverage
- **Defect density**: Bugs per unit of code
- **Defect removal efficiency**: Percentage of defects found before production
- **Mean time to repair**: Average time to fix defects
- **Customer satisfaction**: User feedback and ratings

### Quality Practices

- **Code reviews**: Peer review for quality
- **Automated testing**: Continuous testing in CI/CD
- **Static analysis**: Automated code analysis
- **Performance monitoring**: Monitor system performance
- **User feedback**: Collect and act on user feedback

### Continuous Improvement

- **Retrospectives**: Regular team retrospectives
- **Metrics analysis**: Analyze metrics for trends
- **Process improvement**: Continuously improve processes
- **Knowledge sharing**: Share lessons learned
- **Training**: Continuous learning and skill development

---

> **Note**: For design patterns and architectural principles, see [Software Design](../../software_engineering/concepts/design.md). For DevOps practices and CI/CD, see [DevOps](../../software_engineering/concepts/devops.md).
