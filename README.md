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
    token: ${{ secrets.SRE_EVENTMANAGER_CLIENT_SECRET }}
```

The `token` must have permission to trigger Actions workflows in the
`steve-event-handler` repository (this is provided org-wide today via the
`SRE_EVENTMANAGER_CLIENT_SECRET` repo secret).

