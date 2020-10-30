# container-scan
#
# Use Clair and klar to scan container images
#
# References:
#   Overall process:  https://opensource.com/article/18/8/tools-container-security
#   PostgreSQL     :  https://hub.docker.com/_/postgres
#   Setup Clair    :  https://github.com/quay/clair/blob/master/Documentation/running-clair.md
#   Clair info     :  https://coreos.com/quay-enterprise/docs/latest/clair.html
#     Note info about testing:  curl -X GET -I http://path/to/clair/here:6061/health
#   Clair database connection string:  https://github.com/quay/clair/issues/86

mkdir $PWD/clair_config
curl -L https://raw.githubusercontent.com/coreos/clair/master/config.yaml.sample -o $PWD/clair_config/config.yaml
sudo podman network create clairnet
sudo podman run -d --name clairdb --network clairnet -e POSTGRES_PASSWORD=password postgres:9.6
sudo podman run --privileged=true --net=clairnet --name clair -d -p 6060-6061:6060-6061 -v $PWD/clair_config:/config quay.io/coreos/clair:latest -config=/config/config.yaml
