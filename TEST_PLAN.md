
# TEST PLAN
## 1. Testing Objectives

The primary objectives of testing the Borsibaar application are to:

1. **Ensure functional correctness**: Verify that all features (inventory management, POS, dynamic pricing, authentication) work as specified
2. **Validate data integrity**: Confirm that inventory transactions, sales records, and pricing calculations are accurate and consistent
3. **Verify security**: Ensure authentication and authorization are properly enforced
4. **Assess system stability**: Validate that the application handles edge cases, concurrent operations, and error conditions gracefully
5. **Validate business rules**: Ensure pricing algorithms, stock adjustments, and transaction workflows follow business logic

---

## 2. Testing Levels

### 2.1 Unit Testing

**Backend (Java/Spring Boot)**
- **Scope**: Individual methods in services, controllers, mappers, and utilities
- **Tools**: JUnit 5, Mockito, Spring Boot Test
- **Focus Areas**:
  - Service layer business logic (InventoryService, SalesService, ProductService, etc.)
  - JWT token generation and validation (JwtService)
  - Entity mappers (MapStruct mappers)
  - Security utilities and authentication filters
  - Price correction algorithms (PriceCorrectionJob)
  - Validation logic in DTOs

### 2.2 System Testing

**End-to-End Testing**
- **Scope**: Complete user workflows across frontend and backend
- **Tools**: Playwright
- **Focus Areas**:
  - User authentication flow (OAuth2 login, JWT refresh)
  - Inventory management workflow (add products, adjust stock, view transactions)
  - POS workflow (select station, add items to cart, complete sale)
  - Dynamic pricing updates (verify price changes after sales)
  - Public client view

### 2.3 Acceptance Testing

**User Acceptance Testing (UAT)**
- **Scope**: Real-world scenarios
- **Approach**: Manual testing by end users and/or product owner
- **Focus Areas**:
  - Usability of inventory management interface
  - POS station workflow efficiency
  - Public client view readability
  - Mobile responsiveness


## 3. Test scope

### 3.1 In scope

**Backend**
- Core business logic validation via unit tests (inventory updates, sales transactions, pricing rules, stock adjustments)
- Authentication and authorization logic (JWT handling, role-based access checks, auth filters)
- Data mapping and validation (DTO validation, MapStruct mappings)
- Scheduled/automated logic (e.g., price correction job)
- Error handling and edge cases (invalid input, missing resources, conflict scenarios)

**Frontend**
- End-to-end coverage of the most important user journeys (happy paths + key negative paths)
- UI behavior related to business rules (price updates visible to user, correct totals, correct stock display)
- Basic cross-device checks for critical pages (desktop + mobile responsiveness as part of UAT and/or targeted checks)

**End-to-end workflows (examples)**
- OAuth2 login → token refresh → authenticated usage
- Inventory management: create/update products, adjust stock, verify transactions/history
- POS: station selection, cart operations, completing a sale, receipt/confirmation behavior
- Dynamic pricing: price changes reflected after sales events
- Public client view: read-only view correctness and readability

### 3.2 Out of scope (for this project context)

The following are intentionally deprioritized to keep effort aligned with the application size and expected usage:

- **Extensive integration test suites** (beyond what is implicitly covered by E2E tests)
  - Rationale: for a small application, dedicated integration test layers are considered disproportionate effort compared to strong unit + targeted E2E coverage.
- **Performance/load testing**
  - Rationale: each application instance is expected to serve only a few concurrent clients, so load/performance testing is not expected to provide meaningful ROI at this stage.
- **Deep security testing** (e.g., penetration testing)
  - Note: security is still validated functionally (authn/authz and common failure modes), but specialized security audits are not planned unless risk profile changes.

---
## 4. Test approach

### 4.1 Strategy overview

The testing strategy follows a pragmatic split:

- **Backend-first correctness via unit tests** to cover the majority of business rules with fast, deterministic, highly maintainable tests.
- **Frontend validation via E2E tests** focused on a small set of high-value user stories to ensure the system works end-to-end from the user’s perspective.

This approach is chosen because it maximizes confidence per effort for a small application:
- Unit tests provide strong coverage and fast feedback for backend logic.
- E2E tests validate the integration of frontend + backend for the critical flows.
- A separate, heavy integration-test layer is not prioritized given the project scale.

### 4.2 Test design and prioritization

- **Risk-based selection**: prioritize tests around money-related flows (sales), data integrity (inventory), and security (login/roles).
- **Happy path + critical negatives**:
  - Happy path coverage for each key workflow
  - Negative/edge scenarios where failures would be costly (insufficient stock, invalid inputs, unauthorized access, expired tokens)
- **Business rules as the source of truth**: pricing and inventory rules are asserted primarily at the service layer (unit tests), then verified end-to-end for user-visible correctness.

### 4.3 Automation and execution

**Unit tests (backend)**
- Mock external dependencies where practical (e.g., repositories, external services) to keep tests fast and reliable.
- Favor testing service-layer behavior and business outcomes over implementation details.

**E2E tests (frontend)**
- Use Playwright for browser automation and full workflow validation.
- Keep the E2E suite small and stable: test only the most important user stories to avoid brittleness and long runtimes.
- Include assertions that validate user-visible outcomes (UI state, totals, status messages) and backend-side effects when observable (e.g., updated stock).

**UAT**
- Perform manual validation before release milestones.
- Focus on usability, workflow efficiency, and mobile responsiveness.

### 4.4 Environments and test data (high level)

- Use deterministic, resettable test data for automation (seeded database, fixtures, or API helpers).
- Ensure E2E tests do not depend on data created by previous runs.
- Prefer running automated tests in CI on pull requests (unit tests always; E2E on main and/or nightly if needed).

## 5. Test environment

The test environment defines where and how testing activities are performed. The setup is kept simple and closely aligned with the development environment.

### 5.1 Environments

**Local Development Environment**
- **Used for**:
  - Running backend unit tests
  - Verifying features during development
  - Debugging defects
- **Setup**:
  - Backend: Java + Spring Boot running locally
  - Frontend: Angular development server
  - Database: Local PostgreSQL instance
  - Tools: IDE (e.g., IntelliJ), browser (Chrome/Firefox), optional API tools (e.g., Postman)

**Shared Test / Staging Environment**
- **Used for**:
  - System and end-to-end (E2E) testing
  - Testing integrated frontend and backend functionality
  - User Acceptance Testing (UAT)
- **Setup**:
  - Deployed backend service
  - Deployed frontend application
  - Shared test database
  - Accessible to all team members via browser

---

### 5.2 Test Data

Testing uses separate, non-production data to ensure safety and repeatability.

- **Test data includes**:
  - Sample users with different roles (e.g., admin, regular user)
  - Example products with varying prices and stock levels
  - Sample sales and inventory transactions

- **Guidelines**:
  - Automated tests should create and clean up their own data where possible
  - Alternatively, predefined seed data may be used and reset regularly
  - Tests should not depend on data created by previous test runs

---

### 5.3 Tools and Configuration

| Area | Tools / Technologies |
|------|----------------------|
| Backend unit tests | JUnit 5, Mockito, Spring Boot Test |
| Frontend E2E tests | Playwright |
| API testing (optional) | Postman |
| Database | PostgreSQL (test instance) |
| Browsers | Chrome |
| CI (if available) | CI pipeline (e.g., GitHub Actions) running automated tests |

---

### 5.4 Environment Constraints

- Environments may run on personal or shared machines
- Occasional instability due to active development may occur
- No dedicated environments for performance or security testing are maintained
- Configuration may change during development, and minor environment issues may occur

The focus is on supporting functional and system testing without introducing unnecessary infrastructure complexity.

---

## 6. Entry and exit criteria

Entry and exit criteria define when testing can begin and when it is considered complete for a feature or release.

### 6.1 Entry Criteria

Testing can start when:

- Feature requirements or descriptions are available
- The feature is implemented and deployed to a testable environment
- The application builds and runs without critical startup errors
- Relevant backend unit tests for new or changed logic are written and passing

For system and E2E testing:

- Frontend and backend versions are compatible
- The shared test environment is accessible
- Required test data (users, products, etc.) is available or can be created

---

### 6.2 Exit Criteria

Testing can be considered complete when:

- All planned tests within the defined scope have been executed
- All critical and high-severity defects are fixed or have an agreed workaround
- No open defects block core user workflows (authentication, inventory management, POS, pricing)
- Backend unit tests are passing in the CI pipeline
- Key E2E tests for main user journeys are passing

For UAT:

- Major usability issues have been addressed
- Test users (or team members acting as users) confirm that main workflows are understandable and usable

---

### 6.3 Conditions That Prevent Exit

Testing should continue (exit criteria not met) if:

- Critical defects are still open
- Core workflows (e.g., completing a sale) are failing
- The test environment is too unstable to allow reliable testing

Minor issues may be accepted if they:

- Do not affect core functionality
- Are documented as known limitations

## 7. Roles and responsibilities

Using agile method. Börsibaar project is study project and all team members will try different roles.

**Test Manager**

- Test monitoring and progress control based on the test plan
- Owns and designs test plan, reporting.
- Sets exit criteria and definition of done.
- Presenting testing progress/report

**Developers | Test engineers**

- Test analysis
- Defining the test conditions
- Write and maintain unit tests, integration tests.
- Test design
  - Prioritization of test stories
  - Identification of required test data,  
- Test implementation
  - Developing test procedures and creating test data
  - Writing automated scripts (CI piplines)
  - Creating and maintaining environments
  - Secrets management
  - Monitoring
- Test execution
  - Executing test procedures(manually or automatically)
  - Comparing results with expected results
  - Logging results
  Repeating testis to verify defects

## 8. Risks and assumptions
## 9. Test deliverables
