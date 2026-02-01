
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
## 4. Test approach
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
## 8. Risks and assumptions
## 9. Test deliverables
