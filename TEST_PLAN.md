
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
## 6. Entry and exit criteria
## 7. Roles and responsibilities
## 8. Risks and assumptions
## 9. Test deliverables
