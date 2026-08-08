# killchain

killchain is a security swarm profile that finds reachable exploit chains across shared infrastructure, artifact pipelines, identity systems, and cloud environments.

the profile helps teams identify and prevent multi-step attacks before separate weaknesses form a complete exploit chain. it traces attacker-controlled input through security controls and maps each new capability to the next.

this approach finds reachable, multi-file vulnerabilities that isolated scans can miss. it prioritizes fixes that break the highest-impact attack paths first.

## files

- [`killchain.md`](./killchain.md) contains the complete [security swarm](https://docs.devin.ai/work-with-devin/security-swarm) profile and scan setup guidance.
- this readme contains the package overview and use instructions.

## use the profile

security swarm requires repository access, permission to use [Devin](https://devin.ai/) sessions, and the **use code scans** permission.

1. open **security** in [Devin](https://devin.ai/).
2. select **profiles**.
3. select **create manually**.
4. copy each field from [`killchain.md`](./killchain.md) into the matching profile input.
5. start a scan for one repository with **interactive mode** enabled.
6. review the proposed threat model before the investigation starts.

only enable runtime validation when the repository has a safe, isolated, non-production setup. after the profile is stable, use automatic or incremental scans.

## coverage

killchain investigates:

1. server-side request forgery through fetch services.
2. weak validation during token issuance.
3. plugin and extension execution.
4. unauthenticated writes to shared stores.
5. deserialization before validation.
6. default credentials combined with command injection.
7. file reads through artifact parsing.
8. server-side template injection.
9. secret exposure from the process environment.
10. cloud metadata exposure.
11. workload identities with too many permissions.

## results

a killchain scan produces:

- coverage for all eleven vulnerability classes.
- reachable findings with file and line evidence.
- effective controls that block possible attack paths.
- unresolved leads and missing deployment context.
- complete attack paths that show the capability from each link.
- a remediation order that breaks the highest-impact chains first.

## safety and limits

killchain uses static analysis by default. runtime validation must use harmless canaries and controlled non-production services.

do not call production systems during runtime validation. do not use discovered credentials or expose secret values.

killchain audits the code and configuration that security swarm can access. it does not promise to find unknown vulnerabilities in third-party software.

repository evidence can differ from runtime behavior. reports must identify missing deployment context and validation limits.
