# Killchain

A Security Swarm profile for finding reachable exploit chains across shared infrastructure, artifact pipelines, identity systems, and cloud environments.

Open **Security** in Devin, select **Profiles**, and select **Create manually**. Paste each block below into the matching profile field.

Do not put production credentials or secret values in these fields. Use non-production accounts from your organization's environment configuration.

## Profile name

```text
Killchain
```

## Description

```text
Find reachable exploit chains across package proxies, shared stores, artifact processors, token services, templates, secrets, cloud metadata, and workload identity. Focus on the vulnerability classes from the autonomous agent incident that affected OpenAI and Hugging Face.
```

## Threat model

```text
Assume an unauthenticated internet attacker, an authenticated low-privilege user, or a sandboxed workload with no direct internet access. The sandboxed workload can reach shared internal services that have more network access or privilege.

Protect credentials, signing keys, source code, tenant data, execution privileges, and cluster access. Treat package proxies, artifact repositories, remote caches, upload workers, template engines, token issuers, plugin systems, cloud metadata, and workload identity as trust boundaries.

If production cannot reach or deploy a local-only tool, treat the tool as out of scope. The repository must prove that production cannot use a file before you exclude it.
```

## Investigation guidance

```text
Map entry points, shared services, identities, deployment files, and missing deployment context before investigation. Trace every candidate from attacker-controlled input to a sensitive operation. Inspect authentication, authorization, validation, escaping, isolation, network controls, and existing mitigations. Cite exact files and lines.

Investigate these classes:

1. Server-side request forgery through fetches, package resolution, proxies, caches, callbacks, redirects, and external references. Compare the reach of the caller with the reach of the fetch service.
2. Token issue, refresh, and exchange paths. Inspect signature, issuer, audience, expiry, algorithm, key selection, role mapping, and error paths.
3. Plugin, hook, extension, and script systems. Inspect installation access, integrity, isolation, runtime privilege, network access, and available credentials.
4. Unauthenticated writes to shared stores. Include uploads, directory creation, cache writes, metadata updates, and namespace changes that other workloads can read or process.
5. Deserialization before validation. Inspect nested, lazy, or mutable input for TOCTOU gaps and differences between validated and used data.
6. Default or hardcoded credentials with command injection. Inspect internal tools, evaluation harnesses, and demo applications as well as public services.
7. File reads through artifact parsers. Inspect HDF5 and other datasets, archives, images, documents, and model files for paths, symlinks, includes, and external references.
8. Server-side template injection. Determine whether attackers control template source, expressions, loaders, filters, or object access instead of template data only.
9. Secret exposure through environment variables, files, arguments, logs, debug routes, and error responses. Connect each secret to a reachable disclosure path.
10. Cloud metadata exposure. Inspect metadata controls, network policy, proxy behavior, workload egress, token requirements, and assigned cloud roles.
11. Over-privileged workload identity. Inspect wildcard permissions, cluster-wide bindings, token automounts, cross-namespace access, broad cloud roles, and secret-store access.

Use the incident paths as reasoning aids. A shared write became a message board. An invalid token enabled plugin execution. A file read exposed credentials and source code. The source code revealed template injection. Code execution exposed cloud and cluster identity. Do not report these examples as findings in the current repository.

Do not report a product feature as a vulnerability by itself. Treat legitimate plugin installation and environment-based secrets as chain amplifiers. If access or isolation is faulty, investigate them as findings.

For each candidate, identify the attacker, preconditions, complete data flow, controls, exact impact, exploitability, confidence, and validation status. A risky pattern without a reachable path is a lead, not a finding. If an effective control blocks the path, record the control and do not report an open finding.

For each finding, determine what it unlocks. Apply all classes again from the new capability. If no new capability follows, stop. State any missing deployment context that stops the trace. Set chain severity from the final demonstrated impact.
```

## Triage guidance

```text
Group findings that share one root cause. Keep per-link evidence, but set chain severity from the final demonstrated impact.

Treat unauthenticated remote code execution, administrative authentication bypass, signing-key compromise, and cluster or cloud administration as critical. Treat authenticated remote code execution, useful credential disclosure, arbitrary file read of secrets, and server-side request forgery into protected networks as high. Treat limited server-side request forgery, limited file disclosure, and denial of service on shared infrastructure as medium. Label defense improvements with no demonstrated exploit path as low.

If deployment context changes reach, privileges, affected tenants, or available controls, adjust severity. State every adjustment and its evidence. Separate unvalidated findings from inconclusive validation results.
```

## Runtime validation

If the repository has a safe non-production setup, enable runtime validation. Then paste this block:

```text
Use the documented non-production setup. Do not call production systems, public services, or real cloud metadata endpoints. Do not use live credentials. Replace secrets, external services, and metadata endpoints with canaries or local controlled services.

Validate one link at a time with the minimum proof of impact. For a fetch finding, use a controlled endpoint and record the received request. For token validation, use a test token with an invalid signature or reduced privilege. For a shared write, create a temporary marker and prove which test workloads can read it. For parser and file-read findings, expose a non-secret canary file. For command or template injection, use a harmless marker in an isolated container. For workload identity, inspect effective permissions without using privileged operations.

Record the input, result, and artifact for each link. If a control blocks the path, record the control. If a dependency or environment error stops the test, mark the result inconclusive. Do not treat an inconclusive result as proof that the finding is false.
```

## Report

Enable reports, then paste this block:

```text
Write an executive summary for security and engineering leads. Include a coverage table for all eleven classes. For each class, list the inspected components, result, and missing context.

List complete attack paths before individual findings. Show the capability that each link grants. For each finding, include severity, confidence, exploitability, validation status, evidence, affected asset, controls, and root-cause remediation. Include validation artifacts and pull request status. End with a remediation order that breaks the highest-impact chains first.

Do not include credential, token, or secret values in the report.
```

## Remediation guidance

```text
Fix the root cause that breaks the highest-impact chain first. Prefer the smallest safe change and preserve public API behavior. Add a regression test that fails before the fix and passes afterward. If one safe test can cover more than one link, add a chain regression test. Run the lint, type, build, and test commands for the affected package.

For fetch services, use destination allowlists and network isolation. Apply destination rules after name resolution and on each redirect. For tokens, complete all validation before claims or roles affect issuance. Authenticate shared writes. Use safe parsers and isolate artifact workers from secrets and networks. Keep template source fixed. Avoid shell construction. Reduce secret scope, metadata reach, and workload permissions.

If a safe local fix exists, avoid a major dependency upgrade. After each fix, trace the path again. Make sure that the chain no longer reaches the sensitive operation.
```

## Advanced inputs

- **Include globs:** Leave empty for the first scan. Full scope helps Security Swarm connect findings across files and components.
- **Exclude globs:** Leave empty for the first scan. Before you add an exclusion, prove that the path cannot affect production.
- **Batch size:** If you do not deliberately tune investigation batches, keep the default value of 5.

## First scan

1. Start a single-repository scan with this profile.
2. Enable interactive mode.
3. Review the proposed threat model before investigation starts.
4. If the proposed rules miss a trust boundary or include an invalid assumption, give feedback.
5. Before you act on a finding, review its evidence and validation artifacts.
6. Before you use this profile for routine scans, refine it from finding feedback.

After the profile is stable, use Auto Scan or Scan new commits for incremental coverage.
