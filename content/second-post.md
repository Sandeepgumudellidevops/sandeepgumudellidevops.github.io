Steps to deploy Istio using Istio operator and then deploy Book app to Default namespace.


istioctl operator init

kubectl create ns istio-system

kubectl apply -f - <<EOF\napiVersion: install.istio.io/v1alpha1\nkind: IstioOperator\nmetadata:\n  namespace: istio-system\n  name: example-istiocontrolplane\nspec:\n  profile: default\nEOF

kubectl label namespace default istio-injection=enabled

Use https://istio.io/latest/docs/examples/bookinfo/ to get the below file

kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml -n default
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml -n default

export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

echo $INGRESS_HOST

INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')

export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

echo $GATEWAY_URL

curl -s "http://${GATEWAY_URL}/productpage" | grep -o "<title>.*</title>
