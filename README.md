Automated performance testing and telemetry with Drupal core.

1. Install ddev
1. Clone this repo.
1. `composer install`
1. In the project directory run `ddev config`
1. `ddev start`
1. `cp phpunit.xml.example web/core/phpunit.xml`
1. clone https://github.com/catch56/signoz into a different directory
1. Follow the docker standalone instructions for signoz to get the signoz container up and running, this should boil down to running docker compose up from the right place https://signoz.io/docs/install/docker/
1. Using docker container ls, get the container name for for your Drupal ddev webserver
1. Connect your ddev container to the signoz network: `docker network connect clickhouse-setup_default [your-ddev-webserver-container-id]`
1. signoz has some limits on the community edition dashboard, remove these by running ```UPDATE feature_status SET usage_limit = -1 WHERE name = 'QUERY_BUILDER_PANELS'``` against signoz.db, which can be found in ```signoz/deploy/docker/clickhouse-setup/data/signoz/signoz.db```
1. You can check if this networking step worked by running ddev ssh, then `curl http://otel-collector:4318/v1/traces` - if you get a 405, it worked.
1. Once these steps are working, do the following to run tests and log traces.
1. `ddev ssh`
1. `cd web/core`
1. `../../vendor/bin/phpunit profiles/demo_umami/tests/src/FunctionalJavascript/PerformanceTest.php`
1. Go to http://127.0.0.1::3301 to see the traces in the signoz UI
1. To import a sample dashboard, go to Dashboard -> New Dashboard -> Import JSON then upload from the 'dashboards' directory in this repository.
