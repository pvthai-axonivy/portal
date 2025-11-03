# GitHub Copilot Review Instructions for Axon Ivy Portal

## Project Overview
This is the **Axon Ivy Portal** - the main end-user portal for the Axon Ivy Platform. It's a comprehensive enterprise portal solution built on the Axon Ivy BPM platform.

### Repository Information
- **Repository**: portal
- **Owner**: pvthai-axonivy  
- **Primary Branch**: master
- **License**: Apache License 2.0
- **Current Version**: 13.2.0-SNAPSHOT
- **Supported Versions**: 13.x.x, 12.0.x, 10.0.x

## Technology Stack

### Core Technologies
- **Language**: Java (Axon Ivy Platform)
- **Build Tool**: Maven
- **UI Framework**: PrimeFaces (XHTML/JSF)
- **Testing**: Selenium/Selenide (web-tester)
- **Performance Testing**: JMeter
- **Documentation**: Sphinx/reStructuredText

### Key Dependencies
- Axon Ivy API (ivy-api, ivy-notification-channel-api, ivy-cluster-api)
- Google Gson
- Apache Commons Collections4, Lang3
- PrimeFaces components

## Project Structure

### Main Modules
1. **AxonIvyPortal/portal** - Core portal application (IAR)
2. **AxonIvyPortal/portal-components** - Reusable portal components library (IAR)
3. **AxonIvyPortal/portal-selenium-test** - Selenium-based UI tests
4. **AxonIvyPortal/portal-performance-test** - Performance testing suite
5. **AxonIvyPortal/portal-product** - Product packaging and distribution
6. **AxonIvyPortal/PortalKitTestHelper** - Testing utilities
7. **Showcase/** - Example applications demonstrating portal features
   - InternalSupport
   - portal-components-examples
   - portal-demo-app
   - portal-developer-examples
   - portal-user-examples
8. **Documentation/** - Portal user and developer guides

### Source Directory Structure
- `src/` - Java source code
- `src_hd/` - HTML Dialog (XHTML) files
- `src_dataClasses/` - Data class definitions
- `src_generated/` - Auto-generated code
- `src_wsproc/` - Workflow process definitions
- `src_test/` - Test code (for selenium-test module)
- `webContent/` - Web resources (CSS, JS, images)
- `cms/` - Content Management System files
- `processes/` - Process model files

## Code Review Guidelines

### Java Code Standards

#### Code Formatting
- **Indentation**: Use 2 spaces for indentation (NO TABS)
- **Line Endings**: Consistent line endings
- **Imports**: Remove unused imports - keep imports clean and organized
- **Import Order**: Standard Java import ordering (java.*, javax.*, org.*, com.*, ch.*)

#### Package Structure
- **Core utilities**: `ch.ivy.addon.portalkit.util.*`
- **Business beans**: `ch.ivy.addon.portalkit.bean.*`
- **Enumerations**: `ch.ivy.addon.portalkit.enums.*`
- **DTOs**: `com.axonivy.portal.components.dto.*`
- **Public API**: `com.axonivy.portal.components.publicapi.*`
- **Converters**: `com.axonivy.portal.components.converter.*`
- **Constants**: `com.axonivy.portal.components.constant.*`

#### Code Patterns to Enforce

1. **Security Context Usage**
   - Always use `Sudo.get()` when accessing security-related operations
   - Example pattern from RoleUtils:
   ```java
   public static List<IRole> getAllRoles() {
     return Sudo.get(() -> {
       return ISecurityContext.current().roles().all();
     });
   }
   ```

2. **Null Safety**
   - Use `Objects.nonNull()` and `Objects.isNull()` for null checks
   - Use `CollectionUtils.emptyIfNull()` when working with collections
   - Use `StringUtils.isEmpty()` for string validation
   - Use Apache Commons utilities for safe operations

3. **Utility Classes**
   - Mark utility classes as `final`
   - Provide private constructor: `private UtilityName() {}`
   - All methods should be `static`

4. **Documentation Standards**
   - All public methods must have Javadoc
   - Include `@param` and `@return` tags
   - Document `<p>` sections for detailed explanations
   - Describe special behavior, conditions, and edge cases

5. **Stream API Usage**
   - Prefer Java Streams for collection filtering and transformations
   - Use method references and predicates
   - Example pattern:
   ```java
   private static Predicate<IRole> predicateIsHiddenRole() {
     return role -> Objects.nonNull(role.getProperty(AdditionalProperty.HIDE.toString()));
   }
   
   private static List<IRole> filterRole(List<IRole> roles, Predicate<IRole> predicate) {
     return CollectionUtils.emptyIfNull(roles).stream()
       .filter(predicate)
       .collect(Collectors.toList());
   }
   ```

#### Naming Conventions
- **Classes**: PascalCase (e.g., `RoleUtils`, `AdminSettingBean`)
- **Methods**: camelCase, use descriptive action verbs (e.g., `getAllRoles`, `filterVisibleRoles`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `HIDE_IN_DELEGATION`, `DEFAULT_HIDDEN_ROLES`)
- **Private methods**: camelCase with descriptive names
- **DTOs**: Suffix with `DTO` (e.g., `RoleDTO`, `BusinessDetailsDTO`)
- **Beans**: Suffix with `Bean` (e.g., `AvatarBean`, `CaseActionBean`)
- **Enums**: Singular noun in PascalCase

### Bean (Managed Bean) Patterns
- Beans represent UI backing beans for PrimeFaces/JSF
- Follow JavaBean conventions (getters/setters)
- Ensure proper scope annotations
- Maintain separation between UI logic and business logic

### DTO (Data Transfer Object) Patterns
- Keep DTOs simple and focused on data transport
- Provide constructors that accept domain objects (e.g., `new RoleDTO(IRole role)`)
- Include necessary transformation logic

### Common Anti-Patterns to Flag

1. **Avoid hardcoded values** - Use constants or configuration
2. **Don't catch generic exceptions** - Be specific with exception handling
3. **Avoid deep nesting** - Extract methods for better readability
4. **Don't use raw types** - Always use generics with collections
5. **Avoid mutable static fields** - Ensure thread safety
6. **Don't ignore null checks** - Always validate inputs
7. **Avoid business logic in UI beans** - Delegate to service/utility classes

### Testing Standards

#### Selenium Tests
- Located in `portal-selenium-test/src_test/`
- Use Selenide framework (web-tester dependency)
- Follow page object pattern for UI elements
- Tests should be independent and idempotent

#### Performance Tests
- JMeter-based performance tests in `portal-performance-test/`
- Follow DSL patterns for test definition
- Include realistic load scenarios

### File-Specific Review Focus

#### When reviewing `.java` files:
- ✓ Check indentation is exactly 2 spaces (no tabs)
- ✓ Verify all unused imports are removed
- ✓ Check Javadoc completeness
- ✓ Verify null safety handling
- ✓ Ensure proper use of Sudo for security operations
- ✓ Validate proper exception handling
- ✓ Check for resource leaks (streams, connections)
- ✓ Verify thread safety for shared resources
- ✓ Look for potential performance issues (N+1 queries, inefficient loops)

#### When reviewing `.xhtml` files:
- ✓ Check indentation is exactly 2 spaces (no tabs)
- ✓ Check for proper PrimeFaces component usage
- ✓ Verify backing bean bindings
- ✓ Ensure accessibility attributes (ARIA labels)
- ✓ Check for proper i18n/localization
- ✓ Validate responsive design considerations

#### When reviewing `pom.xml` files:
- ✓ Check indentation is exactly 2 spaces (no tabs)
- ✓ Verify version consistency (current: 13.2.0-SNAPSHOT)
- ✓ Check for proper dependency scopes
- ✓ Ensure parent POM reference is correct
- ✓ Validate repository configurations

#### When reviewing process/workflow files:
- ✓ Ensure proper error handling
- ✓ Verify business logic correctness
- ✓ Check for proper role/permission assignments

### Security Considerations

1. **Role-Based Access Control**
   - Always respect HIDE and HIDE_IN_DELEGATION properties
   - Use proper filtering methods (filterVisibleRoles, filterHiddenRoles)
   - Validate user permissions before operations

2. **Property Access**
   - Use `AdditionalProperty` enum for standard properties
   - Validate property values before use

3. **Data Sanitization**
   - Sanitize user inputs in UI components
   - Prevent XSS vulnerabilities in XHTML
   - Validate and escape data in JSF expressions

### Localization & Internationalization
- Portal supports multiple languages via Weblate
- All user-facing strings should be externalized
- Use proper CMS/resource bundle references
- Never hardcode user-visible text

### Performance Considerations

1. **Lazy Loading** - Use lazy initialization for expensive operations
2. **Caching** - Cache frequently accessed data appropriately
3. **Stream Processing** - Prefer streams for large collections
4. **Database Queries** - Optimize Ivy API calls, avoid N+1 patterns
5. **UI Rendering** - Minimize re-renders in PrimeFaces components

## Build & CI/CD Context

### Jenkins Pipelines
- Multiple Jenkinsfiles in `build/` directory
- CI types: integration, gui-test, performance-test, document-screenshot
- Docker-based build environments

### Packaging
- IAR (Ivy Archive) format for modules
- Product assembly via `portal-product/`
- Distribution via Axon Ivy Market

## Documentation Standards

### Code Comments
- Use `//` for single-line comments
- Use `/* */` for multi-line explanations
- Javadoc (`/** */`) for all public APIs

### README Files
- Each major module should have a README
- Include setup, usage, and contribution guidelines

## Quality Metrics to Check

1. **Code Coverage** - Adequate test coverage for new features
2. **Complexity** - Keep cyclomatic complexity low (< 10 per method)
3. **Maintainability** - Code should be self-documenting
4. **DRY Principle** - Avoid code duplication
5. **SOLID Principles** - Especially Single Responsibility

## Common Review Scenarios

### When Adding New Features:
1. Check if public API needs updates
2. Verify backward compatibility
3. Ensure proper documentation
4. Add appropriate tests
5. Update showcase examples if applicable

### When Fixing Bugs:
1. Verify root cause is addressed
2. Add regression test
3. Check for similar issues in codebase
4. Update documentation if behavior changes

### When Refactoring:
1. Ensure no functional changes
2. Verify all tests still pass
3. Check performance impact
4. Update comments/documentation

## Specific Review Prompts

When reviewing code, consider:
- "Does this follow the Axon Ivy Portal coding patterns?"
- "Is security properly handled with Sudo.get() where needed?"
- "Are all edge cases and null values handled?"
- "Is this properly documented for other developers?"
- "Would this work correctly in a multi-tenant environment?"
- "Is this internationalization-ready?"
- "Does this maintain backward compatibility?"
- "Are there potential performance bottlenecks?"

## Contributing Guidelines Reference

All changes should:
1. Be tested and working
2. Target the `master` branch
3. Follow the code standards outlined above
4. Include appropriate documentation
5. Consider localization needs

## Additional Context

### Axon Ivy Platform Specifics
- **IRole, IUser, ISecurityContext**: Core security interfaces
- **IAR**: Ivy Archive - deployable module format
- **Process Elements**: Workflow definitions in visual designer
- **HTML Dialogs**: XHTML-based UI components

### Portal Architecture Patterns
- **Component-based**: Reusable components in `portal-components`
- **Modular**: Clear separation between modules
- **Extensible**: Public APIs for customization
- **Showcase-driven**: Examples demonstrate capabilities

---

*This instruction file should be updated as coding standards evolve and new patterns emerge in the portal codebase.*
