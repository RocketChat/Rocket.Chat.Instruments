# Rocket.Chat.Metrics

![](https://i.imgur.com/EatD1VO.png)

This repository contains a basic Monitoring setup [Rocket.Chat](https://github.com/RocketChat/Rocket.Chat) setups, based on Grafana and Prometheus. It currently shows and visualizes the following application metrics/information:

- _General totals_ (users online/away, DDP users, diffs based on time range)
- _Metrics_ (requests and total size per instance)
- _NodeJS_ (active handles, requests, event loop lag, heap used, per instance heap, garbage collector)
- _DDP rate limiter_ (by method, by type, by userId, by connectionId)
- _Rocket.Chat Data_ (messages sent, total sent, user presence, users & sessions, Oplog, push queue, Meteor facts, total users, total rooms, notification per minute per notification type)
- _Meteor_ (methods total time, methods time, method calls per minute)
- _Subscriptions_ (subscription total time, subscription time, subscription calls per minute)
- _Callbacks & Hooks_ (callbacks total time, callbacks time, callback calls per minute, hooks total time, hooks time, hook calls per minute)
- _REST API_ (REST total time, REST time, REST calls per minute)

## Installation

1. Make sure you meet all [requirements](#requirements--dependencies).

2. Clone this repository:

    ```shell
    git clone https://github.com/RocketChat/Rocket.Chat.Metrics /opt/docker/Rocket.Chat.Metrics
    cd /opt/docker/Rocket.Chat.Metrics
    ```

3. Adjust the default Prometheus configuration accoring the [usage information below](#prometheus) (either for Docker-based or static Rocket.Chat setups).

4. Create and start up containers using `docker-compose`. Make sure to include *both* compose files:

    ```
    docker-compose -f docker-compose.yml -f docker-compose.monitoring.yml up -d
    ```

5. Access your Grafana instance via `http://${HOST_IP}:3300`.

## Configuration

### Prometheus

#### Monitor a single Rocket.Chat server

To monitor one or multiple static Rocket.Chat systems ("static" in terms of the Prometheus metrics endpoint doesn't change and is always available under the same address) you just have to add each of your Rocket.Chat hostnames in the [`./config/prometheus/prometheus.yml`](config/prometheus/prometheus.yml) file like this:

```yaml
- job_name: rocketchat_static
  static_configs:
  - targets:
    - rocketchat1.company.com:9458
    - rocketchat2.company.com:9458
```

Where `rocketchat1.company.com` and `rocketchat2.company.com` are reachable endpoints from within the Prometheus container.

#### Monitor a multi-instance or Docker-based Rocket.Chat server/setup

In case you have a Docker-based setup that might scale additional application instances in and out on the fly, you can use Prometheus DNS discovery method. Please make sure that the Prometheus container is within the same Docker network as your Rocket.Chat application containers. Use a configuration like this:

```yaml
- job_name: rocketchat_docker
  scrape_interval: 30s
  dns_sd_configs:
  - names: ["rocketchat"]
    type: A
    port: 9458
```

Make sure to update `rocketchat` with the container name of your Rocket.Chat application container.

### Grafana

#### Using Grafana

As soon as the containers are up and running, you can access the Grafana UI via the configured port and select the autoprovisioned dashboard "_Rocket.Chat Metrics_".

#### Default credentials

#### Additional system and Docker monitoring dashboards (optional)

- cadvisor
- node-exporter

## Troubleshooting

### I do not want to access the Prometheus Web UI.

By default the port for Prometheus' Web UI is exposed for easier metric troubleshooting. To disable it simple remove the `ports` mapping in the `prometheus` section of the compose file:

```
    ports:
     - 9090:9090
```

## Contributing

1. Fork it
2. Create your feature branch:

    ```shell
    git checkout -b feature/my-new-feature
    ```

3. Commit your changes:

    ```shell
    git commit -am 'Add some feature'
    ```

4. Push to the branch:

    ```shell
    git push origin feature/my-new-feature
    ```

5. Submit a pull request

## Requirements / Dependencies

* Up and running Rocket.Chat system
* Prometheus enabled in Rocket.Chat (Administration -> Logs -> Prometheus)
* Docker

## License

[MIT](LICENSE)
