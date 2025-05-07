# Gitleaks playground

I implemented this playground to test how to use and configure *gitleaks*.

I also have a *git hook* *pre-commit* configuration.

```sh
$ mise install
$ git config core.hooksPath git-hooks
```

```sh
$ gitleaks git -v

    ○
    │╲
    │ ○
    ○ ░
    ░    gitleaks

9:13PM INF 1 commits scanned.
9:13PM INF scan completed in 5.51ms
9:13PM INF no leaks found
```

I generate a ssh key that should be detected by *gitleaks*:

```sh
$ ssh-keygen -t rsa -b 4096 -f ./dummy-key -N ""
```

I check that I can't commit a secret:

```sh
$ git add dummy-key
$ git commit -m "WIP"
Running gitleaks to check for secrets leaks...
...
Entropy:     6.007368
File:        dummy-key
Line:        1
Fingerprint: dummy-key:private-key:1

10:03PM INF 1 commits scanned.
10:03PM INF scan completed in 7.94ms
10:03PM WRN leaks found: 1
Gitleaks detected potential issues. Commit aborted.
```

```sh
$ git restore --staged dummy-key*
```

I generate a file that contains a password:

```sh
$ cat <<'EOF' > secret
export PASSWORD="aeFeaxoo3phaikae"
EOF
```

```sh
$ gitleaks --no-banner dir -v
Finding:     export PASSWORD="aeFeaxoo3phaikae"
Secret:      aeFeaxoo3phaikae
RuleID:      generic-password
Entropy:     3.077820
File:        secret
Line:        1
Fingerprint: secret:generic-password:1

10:50PM INF scan completed in 3.55ms
10:50PM WRN leaks found: 1
```

```
$ mv secret .secret
```

`.secret` is ignored in `.gitleaks.toml`.

```sh
$ gitleaks dir --no-banner -v
11:29PM INF scanned ~478 bytes (478 bytes) in 5.07ms
11:29PM INF no leaks found
```
