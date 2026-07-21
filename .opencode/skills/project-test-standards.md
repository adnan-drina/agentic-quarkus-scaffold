# Skill: Project test standards

How this team tests. Apply on every change.

- Every REST endpoint gets a `@QuarkusTest` with RestAssured covering the
  happy path and at least one failure path.
- Every service ships a `HealthResourceTest` asserting `/q/health` returns
  200 with `status: UP` — the platform probes depend on it.
- List endpoints assert both content and size, leveraging the
  repository's deterministic seed order:
  `.body("itemId", contains("329299", "329199", "165613"))` plus
  `.body("$", hasSize(3))`. (Reference: `coolstore-inventory-service`.)
- Test names describe behavior (`returnsNotFoundForUnknownClaim`), not
  methods (`testGet2`).
- Never weaken an assertion, delete a test, or raise a threshold to make a
  gate pass — fix the code. If a gate failure looks wrong, say so and stop.
- Business logic lives in `@ApplicationScoped` services tested with plain
  JUnit where possible; keep `@QuarkusTest` for the HTTP boundary.
- Decimal JSON fields (prices, quantities): never assert with exact float
  equality — Jackson may render `10.00` as `10.0`. Use the BigDecimal
  comparator and write it once, correctly:

  ```java
  import static org.hamcrest.Matchers.comparesEqualTo;
  // config once per test class:
  // RestAssured.config = RestAssured.config().jsonConfig(
  //     jsonConfig().numberReturnType(BIG_DECIMAL));
  .body("price", comparesEqualTo(new BigDecimal("10.00")))
  ```
- External HTTP dependencies are mocked in tests with WireMock — the
  canonical dependency is `org.wiremock:wiremock` in `test` scope; tests
  must pass with no live downstream service available.
- `mvn -q test` must pass locally before any push; the platform pipeline's
  SonarQube gate fails on any new issue.
