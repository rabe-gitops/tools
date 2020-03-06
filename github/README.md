1. Generate a Personal Access Token on GitHub with `repo`, `hooks`, `user:email` accesses

2. Create a new GitHub server, creating:
  * a Secret Text credential with the previously generated token
  * flagging "Manage hooks" (the Jenkins URL setting must be properly configured)

3. If your Jenkins certificate is self-signed, you need to go to the Webhooks configuration on your GitHub repo and make sure that the insecure hook delivery is flagged. The "Manage hooks" Jenkins settings (see point 2) should be disabled in order to avoid the SSL verification setting reset

4. Create multi-branch declarative pipelines (preferably using the BlueOcean UI plugin), and enable branches, tags, and changes discovery on the pipeline configuration (using the Jenkins Classic UI). Moreover, after installing the [Basic Branch Build Strategies](https://plugins.jenkins.io/basic-branch-build-strategies) plugin, the build triggers can be configured for each discovered event.
