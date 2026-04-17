# Environment Topology and Defaults

This file captures the current working topology for the combined **OpenClaw + Codex** setup.

## Default operating assumption

The default target for most work is the **MSI workstation**, because that is where:

- OpenClaw is installed
- Codex is installed
- combined-stack troubleshooting will usually happen first

Do **not** automatically pull `clinic-server` into every troubleshooting run.

Only include `clinic-server` when the symptom explicitly involves:

- paired-node behavior
- remote execution or remote tools
- Tailscale reachability between machines
- node auth / pairing / token issues
- a problem that is clearly happening on the clinic server itself

## Naming note

The user states that the primary workstation is **MSI** and that its Tailscale identity is **old-msi**.

The current Tailscale admin screenshot appears to show a node named **`msi-old`**.

Until this naming mismatch is explicitly cleaned up, treat these as references to the same intended primary workstation and verify the exact node label before running host-specific commands or writing permanent instructions.

## Machine inventory

### Primary combined-stack host
- Human name: **MSI**
- Role: primary OpenClaw + Codex workstation
- Tailscale node label seen in admin screenshot: **`msi-old`**
- Tailscale IP from admin screenshot: **100.104.241.118**
- Default assumption: this is the first machine to inspect for OpenClaw + Codex issues

### Secondary / occasional node
- Machine: **`clinic-server`**
- Tailscale IP: **100.69.56.78**
- Role: secondary paired node only
- Default rule: do not include in every run unless the symptom points there

### Other known Tailscale machines
- Machine: **`msi`**
  - Tailscale IP: **100.110.169.62**
  - Notes: marked with **Funnel** in the admin screenshot
- Machine: **`s25`**
  - Tailscale IP: **100.88.76.76**
  - Notes: Android device

## Operating guidance

### When starting diagnosis
1. Ask whether the symptom is on the MSI workstation first.
2. Treat `clinic-server` as optional scope unless the issue is clearly node-related.
3. Use Tailscale IPs only when they are actually needed for reachability checks, pairing, or remote targeting.
4. When recording incidents, always note:
   - human machine name
   - Tailscale node label
   - Tailscale IP
   - whether the failure was local, remote, or handoff-related

### When writing durable records
Always prefer explicit phrasing like:

- “Primary host: MSI workstation (`msi-old` in Tailscale admin screenshot)”
- “Secondary node: `clinic-server` (100.69.56.78)”
- “Other known nodes: `msi` (100.110.169.62), `s25` (100.88.76.76)”

This prevents future confusion between the local workstation name and the Tailscale node label.
