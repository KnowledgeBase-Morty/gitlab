# Gitlab

## CI/CD (Gitlab)
hosts:
1. Make sure Windows `hosts` file (`notepad c:\windows\System32\drivers\etc\hosts`) has localhost (`127.0.0.1`) added to `gitlab.example.com`.

docker-compose.yaml:
1. Set $GITLAB_HOME (`export GITLAB_HOME=/srv/gitlab`)
2. Get docker-compose.yaml file from `https://docs.gitlab.com/ee/install/docker.html` (first example)
3. RUN: `docker compose up -d`

In docker container:
1. Make sure the `external_url` is set to `http://gitlab.example.com` (`vi /etc/gitlab/gitlab.rb`). Run `gitlab-ctl reconfigure` if a change was made. Try loggin in (username: `root`, password: `/etc/gitlab/initial_root_password`) from local browser at `http://gitlab.example.com`. If it does not work, try `http://localhost`. If that doesn't work either, please see the `Loggin In` section.
2. Create/Import a repository (on the UI).
3. Add a `.gitlab-ci.yml` file to the root directory of the repository (See `https://docs.gitlab.com/ee/ci/yaml/gitlab_ci_yaml.html`)
4. Create a `gitlab-runner` (Follow instructions at `http://gitlab.example.com/root/<repo-name>/-/settings/ci_cd#js-runners-settings`). Make sure to select `docker` and once a runner is registered, edit the config file (`vi /etc/gitlab-runner/config.toml`) and add `network_mode = "gitlab_default"` below `shm_size = 0`.
5. Settings > CI/CD > Variables: Set the `DOCKER_AUTH_CONFIG`={
    "auths": {
        "https://ctm-docker.art.sdl.usu.edu": {
            "auth": "(`echo -n<username>:<password> | base64`)",
            "email": "`<insert-email>`"
        }
    }
}

Loggin in:
1. Find the default password in `/etc/gitlab/initial_root_password`.
2. Try to login at `http://gitlab.example.com`
3. If password is incorrect do steps 6-10, if not, you're all set!
4. Run the ruby rails console: `gitlab-rails console -e production`
5. Run: `u = User.find_by(username: 'root')`
6. Run: `u.password = 'password'`
7. Run: `u.password_confirmation = 'password'`
8. Run: `u.save!`
9. Try loggin in again with username: root and password: password.

Test it:
1. Clone the repo.
2. Make a change and push the changes up.
3. Navigate to the `http://gitlab.example.com/root/<repo-name>/-/pipelines`
4. Make sure the pipeline is triggered and the the `.gitlab-ci.yml` builds successfully.
