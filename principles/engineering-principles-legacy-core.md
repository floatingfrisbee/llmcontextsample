# Engineering Principles for Legacy and Core Product Areas

## Summary
When changing older or core product code, prefer engineering clarity over clever architecture. The goal is to improve behavior while preserving readability, maintainability, observability, and ease of rollback.

## Principles
- Understand the existing code path before designing the change.
- Use established product abstractions and naming patterns unless there is a concrete reason not to.
- Limit new concepts. Avoid creating frameworks, generic layers, nested settings hierarchies, event systems, or cache schemes for a narrow feature.
- Keep controllers thin. Controllers should adapt HTTP concerns and orchestrate calls; they should not accumulate domain rules simply because an endpoint needs them.
- Keep business/customer-facing policy separate from low-level operational knobs.
- Prefer small, explicit option classes and DTOs over broad objects that become junk drawers.
- Make changes easy to roll back: avoid unnecessary migrations, broad renames, global rewrites, or irreversible side effects.
- Preserve existing lifecycle behavior unless changing it is the point of the work.
- Add targeted logging around decision points, not noise around every operation.
- Add focused tests around the behavior being changed, especially edge cases and fallback behavior.
- If a design starts to feel ad hoc or overly clever, step back and reshape it around the product's existing patterns.

## Static Service Ban
- Do not introduce static service classes or static service entry points.
- Services must expose an interface and be supplied through dependency injection so dependencies remain explicit, replaceable, and testable.
- Static service usage requires discussing the specific scenario with Jaspreet and agreeing on an exception before implementation.
- Private static helper methods inside an instance service are acceptable when they are pure implementation details and do not hide dependencies.

## Purpose-Built Service Rule
- Do not move controller complexity into miscellaneous manager, helper, enricher, processor, or do-everything services.
- Define services around one explicit capability or use case, behind a contractual interface in the project that owns that behavior.
- A use-case service may orchestrate focused collaborators, but it should not absorb their implementation details.
- Keep external lookup/failure policy, domain mutation, formatting, persistence, and HTTP adaptation in separate components when they have different reasons to change.

## Thin Controller Rule
Controllers may own routing, model binding, authorization, HTTP status selection, endpoint logging, and small-scale orchestration.

Do not put domain validation, normalization, merge/update semantics, persistence shaping, mapping rules, or provider-neutral/provider-specific business behavior directly in controllers. Put that behavior in a focused service or domain component in the project that owns it, then have the controller call the abstraction.

For CRM work:
- Provider-neutral CRM rules and contracts belong in `Dot.Crm`.
- Provider-specific behavior belongs in the provider project, such as `Dot.Crm.Salesforce`.
- Falcon UI controllers should remain HTTP adapters.

Before adding more than a small amount of controller logic, stop and identify the behavior's owning service and project. Test business rules at that service/domain boundary; keep controller tests focused on HTTP behavior and orchestration.

## Practical Checklist
Before implementing in core/legacy areas:
- Identify the true owner system and avoid coupling adjacent systems just because names overlap.
- Decide whether the setting is customer/admin-facing policy or appsettings-style operational configuration.
- Define the smallest persisted contract needed today.
- Define when a setting change takes effect.
- Define how invalid/missing settings behave.
- Define logs that will help diagnose behavior after deployment.
- Define rollback behavior before coding.

## Project Reference Direction Rule
- Treat every new or changed project reference as an architectural decision, not as a compile fix.
- Before adding a `ProjectReference`, check dependency direction: shared/domain/provider projects must not reference application/UI projects such as `Dot.Falcon.UI` or app-specific projects such as `Dot.Falcon` unless Jaspreet explicitly approves a narrow exception.
- Preferred direction is app/composition projects reference shared/domain/provider projects, not the reverse.
- If moving code creates a tempting upward reference, stop and split the code instead: keep provider/domain behavior in the provider project and keep app-specific adapters, logging action enums, current-user providers, and HTTP/controller orchestration in the app layer.
- In reviews, scan project references for accidental upward dependencies, especially after moving files between projects.

## Base Project Implementation Inventory Rule
- Generic/base projects must not maintain inventories of concrete implementations owned by downstream/provider projects.
- A base project may define generic contracts and accept opaque ids such as strings, but it should not list known providers, vendors, plugins, integrations, applications, or runtime implementations.
- Provider/project-specific ids, display names, defaults, capabilities, endpoint paths, and registration metadata belong in the implementation/provider project or app composition layer that owns them.
- Generic tests should use neutral ids such as `provider-a` and `provider-b`; provider-specific constants belong only in provider-specific tests.
- If a base project starts to need a list of implementations, stop and introduce/extend composition or registry discovery from registered implementations instead of adding constants/enums to the base project.

## Significant Check-In Review Checklist
These are Jaspreet's typical pre-commit checks. Before a significant check-in, do a deliberate review pass and record any material findings in the handoff/commit notes.

Checklist:
- Confirm project references and dependency direction are intentional; shared/domain/provider projects must not acquire accidental references to application/UI projects.
- Confirm code still lives in the owning project/layer: controllers stay thin, app layers orchestrate app-specific composition, provider projects own provider-specific behavior, and generic domain projects stay provider-neutral.
- Walk the affected end-to-end flows, especially configuration, mapping, value selection, persistence/save, auth, and user-visible error paths.
- Check for hardcoded provider, environment, URL, identity, data-shape, or customer assumptions outside the owning provider/configuration boundary.
- Verify recently created code is purpose-built, SOLID, and readable; avoid broad managers/helpers, hidden static services, or new framework concepts unless explicitly discussed.
- Confirm existing patterns are respected for dependency injection, options/settings, logging, validation, controller delegate execution, DTOs, tests, and frontend form behavior.
- Confirm logging exists at useful decision/failure points and does not leak secrets or sensitive payloads.
- Confirm tests cover the changed behavior, especially fallback/default behavior, persistence shape, validation, and boundary contracts.
- Run targeted static scans/tests where practical; if full build/test is deferred, say exactly what was and was not run.
- Call out residual risks and cleanup opportunities separately from blockers, so check-ins are stable without hiding known debt.

## Settings Schema Change Checklist
Treat options/settings classes as persisted schemas, not ordinary DTOs. The settings store persists option values by option class name and property name, so small refactors can become data migrations.

Before changing an options class:
- Class names and property names are persistence keys. Do not rename them without an explicit compatibility or migration plan.
- Prefer additive properties with safe CLR defaults when possible.
- For renamed settings, temporarily read from the old key or provide a deliberate migration path.
- For type changes, add tests that fetch old persisted values and confirm the new code behaves safely.
- For list/object properties serialized as JSON, add tests with representative old JSON payloads.
- Do not log setting values, secrets, tokens, connection strings, or raw JSON payloads. Log keys, option type, property name, target type, and failure category instead.
- Make missing or invalid important settings visible at the owning service boundary rather than relying on silent defaults.
- For sensitive settings, preserve write-only behavior: fetch responses may report presence, but should not return the secret value.

## Preferred Deep Pass Trigger
When Jaspreet says something like "do the deep pass with my preferred checks", "run my preferred check-in checks", "run my pre-commit checks", or "do the significant check-in review", treat it as a request to apply the Significant Check-In Review Checklist above (his typical pre-commit checks).

Default behavior for that request:
- Review boundaries, references, ownership, and dependency direction.
- Walk the affected end-to-end flows.
- Check SOLID/readability, controller thinness, logging, tests, and existing-pattern fit.
- Look for hardcoded provider/environment/customer assumptions.
- Patch only concrete issues that are clearly in scope; otherwise report findings, residual risks, and recommended follow-ups.
