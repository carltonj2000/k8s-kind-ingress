# Kind Ingress On Single Machine

```bash
# start kind with ingress enable in config
kind create cluster --config kind.config
# start the ingress provider
kubectl apply --filename https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
# wait for ingress provider to be running
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
# run a test container hello
kubectl run hello \
  --expose \
  --image nginxdemos/hello:plain-text \
  --port 80
# setup ingress route to container
kubectl create --filename ingress.yaml
# get kind docker ip address
docker container inspect kind-control-plane \
  --format '{{ .NetworkSettings.Networks.kind.IPAddress }}'
# add it to /etc/hosts
# 172.18.0.2      hello.carltonj2000.com
curl hello.carltonj2000.com
# now with modifying /etc/hosts specify host to ip mapping during run
docker run \
  --add-host hello.carltonj2000.com:172.18.0.2 \
  --net kind \
  --rm \
  curlimages/curl:7.71.0 hello.carltonj2000.com
```

## Creation History

The content of this repository is based
[How to Test Ingress in a kind cluster](https://dustinspecker.com/posts/test-ingress-in-kind/)
article.
