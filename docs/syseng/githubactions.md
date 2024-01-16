# Github Actions
## Operations

## Troubleshooting/Debugging
### OIDC
Use [OIDC Debugger](https://github.com/github/actions-oidc-debugger) in workflow to to print out OIDC claims included in Github Actions's JWT 
```yaml
    steps:
    - name: Checkout actions-oidc-debugger
      uses: actions/checkout@v4
      with:
        repository: github/actions-oidc-debugger
        ref: main
        token: ${{ secrets.TOKEN }}     # Github Personal Access Token
        path: ./.github/actions/actions-oidc-debugger

    - name: Debug OIDC Claims
      uses: ./.github/actions/actions-oidc-debugger
      with:
        audience: 'https://github.com/github'

```
