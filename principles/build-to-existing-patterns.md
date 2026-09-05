# Build to Existing Patterns

When working in an existing product, first understand the environment and build to its patterns. Prefer elegant, understandable code that composes with the product's existing architecture, UI conventions, configuration systems, logging, authentication, testing, and deployment model.

Avoid hardcoded assumptions about UI dimensions, runtime environment, infrastructure, authentication, data shape, or provider behavior. When complexity exists, model it explicitly and use the product's established abstractions rather than local one-off fixes. If the current approach starts to feel ad hoc, step back, inspect the surrounding system, and reshape the implementation around durable patterns before continuing.
