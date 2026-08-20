# shared-workflows-actions
Has shared workflow code which projects can call.

## Actions

### `notify-deployment`

A composite action that projects call right after deploying, to emit a
"just deployed to an environment" event. It triggers the `deployment-event.yml`
`workflow_dispatch` workflow in [`steve-event-handler`](https://github.com/steve-sre-event-playground/steve-event-handler),
passing details about the fictional deployment (application, environment,
version, commit SHA, actor).

Usage from a calling project's workflow:

```yaml
- name: Notify event handler of deployment
  uses: steve-sre-event-playground/shared-workflows-actions/.github/actions/notify-deployment@main
  with:
    application: example-project-1
    environment: staging
    version: 1.0.42
    commit-sha: ${{ github.sha }}
    app-id: ${{ secrets.SRE_EVENTMANAGER_APP_ID }}
    app-private-key: ${{ secrets.SRE_EVENTMANAGER_PRIVATE_KEY }}
```

The action mints a short-lived installation token for the
`example-eventmanager` GitHub App (from `app-id`/`app-private-key`) and uses
it to call `gh workflow run` against `steve-event-handler`. A plain OAuth
client secret cannot be used for this - it only supports interactive user
login flows, not calling the Actions API. Triggering `deployment-event.yml`
as a `workflow_dispatch` (rather than a `workflow_call` reusable workflow)
also means that run executes fully inside `steve-event-handler`'s own
repository context, so any secrets it needs stay private to it.

Requires the `SRE_EVENTMANAGER_APP_ID` and `SRE_EVENTMANAGER_PRIVATE_KEY`
repo secrets on the calling repo, scoped to the `example-eventmanager` app
installation which has `actions: write` permission on `steve-event-handler`.

