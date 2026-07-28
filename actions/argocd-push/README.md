# argocd-push

Pushes ArgoCD/kustomize commits to a **ruleset-protected** branch (main/master/dev/develop)
without disabling the ruleset. Works by minting a GitHub App installation token — the App
is in the ruleset bypass list, `github-actions[bot]` is not.

## Why a GitHub App (and not a PAT)

Ruleset bypass lists match **actors**. A PAT (`ORG_GIT_TOKEN`) always acts as the user who
owns it, so it only bypasses if that user is in the bypass list — and fine-grained PATs
often can't bypass rulesets at all. A GitHub App is a first-class bypass actor. That's why
the PAT attempt failed and the App works.

## One-time org setup

1. **Create a GitHub App** (org → Settings → Developer settings → GitHub Apps → New).
   - Repository permissions: **Contents: Read and write**.
   - No webhook needed.
   - Generate a **private key**, note the **App ID**.
2. **Install the App** on the org (all repos, or the ones that deploy).
3. **Add the App to each branch ruleset's bypass list** (Settings → Rules → your ruleset →
   Bypass list → add the App). This is the step that lets it push.
4. **Org secrets** (Settings → Secrets → Actions):
   - `ARGOCD_PUSHER_APP_ID` = the App ID
   - `ARGOCD_PUSHER_PRIVATE_KEY` = the private key (full PEM)

## Usage

In a deploy job, after editing kustomize files:

```yaml
      - uses: pedidopago/cicd/actions/argocd-push@master
        with:
          app_id: ${{ secrets.ARGOCD_PUSHER_APP_ID }}
          private_key: ${{ secrets.ARGOCD_PUSHER_PRIVATE_KEY }}
          branch: ${{ github.ref_name }}
          commit_message: update kustomize image tags
```

Replaces the `git commit` + `ad-m/github-push-action` steps that push with
`GITHUB_TOKEN`/`ORG_GIT_TOKEN`.
