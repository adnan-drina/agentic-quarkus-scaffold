# Skill: Quarkus REST conventions

How this team builds REST endpoints. Apply on every endpoint change.

- All resources live under the `/api/` path prefix.
- The path segment is named for the domain/service, not the entity type:
  `@Path("catalog")` for the catalog service (as inventory exposes
  `/api/inventory`) — never `@Path("products")` after the `Product` record.
  The spec's stated path is authoritative; do not re-derive it from the model.
- Resource classes end in `Resource`, live in `com.demo.<domain>`, and use
  constructor injection only — never field injection (`@Inject` on fields).
- Request/response bodies are records or simple POJOs serialized with
  Jackson; never expose entities directly.
- Errors return RFC-7807-style JSON (`status`, `title`, `detail`) with
  `Content-Type: application/problem+json` — no empty catch blocks, no
  stack traces in responses. Map exceptions with the Quarkus-native
  `@ServerExceptionMapper` (package
  `org.jboss.resteasy.reactive.server`), which is always discovered:

  ```java
  @ServerExceptionMapper
  public Response mapNotFound(ProductNotFoundException e) {
      return Response.status(404)
          .type("application/problem+json")
          .entity(Map.of("status", 404, "title", "Not Found",
                         "detail", e.getMessage()))
          .build();
  }
  ```

  Do not fall back to inlining error responses in resource methods, and
  never use `@RegisterProvider` — that annotation belongs to the
  MicroProfile REST *Client* and does not exist in `jakarta.ws.rs.ext`.
- Money and prices are `BigDecimal`, constructed from string literals
  (`new BigDecimal("34.99")`) — never `double` literals (they do not
  convert implicitly and lose precision). Jackson serializes `BigDecimal`
  as a plain JSON number.
- Log through `org.jboss.logging.Logger` (one static logger per class);
  `System.out.println` is forbidden.
- Every endpoint gets an OpenAPI-visible description: meaningful method
  names, `@Produces`/`@Consumes` declared explicitly.
- Update the README API table in the same change as any endpoint change.
