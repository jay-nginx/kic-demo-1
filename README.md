# kic-demo-1
KIC Demo - Rate Limiting, Access Control List, API Key Authentication &amp; Header Based Routing



Install KIC via HELM:

helm install my-release nginx-stable/nginx-ingress --set controller.image.repository=nginx-plus-ingress --set controller.nginxplus=true --set controller.enableSnippets=true --set controller.enablePreviewPolicies=true

Deploy the Demo, Tea/Coffee Application:

	kubectl apply -f cafe.yaml
	kubectl apply -f cafe-secret.yaml
	kubectl apply -f cafe-virtual-server.yaml

	curl -H "Host: cafe.example.com" https://192.168.0.3/tea --insecure



Rate Limiting: Via Policy

	kubectl delete -f cafe-virtual-server.yaml

	kubectl apply -f cafe-virtual-server-1.yaml

	kubectl apply -f rate-limit.yaml

	curl -H "Host: cafe.example.com" https://192.168.0.3/tea --insecure

	You should get 503 - Service Temporarily Unavailable message


Access Control List: Via Policy

	kubectl delete -f cafe-virtual-server-1.yaml

	kubectl apply -f cafe-virtual-server-2.yaml

	kubectl apply -f access-control-policy-allow.yaml

	kubectl apply -f access-control-policy-deny.yaml

	curl -H "Host: cafe.example.com" https://192.168.0.3/coffee --insecure

	You should get 403 - Forbidden Error

API Key Authentication: Via Snippets

	kubectl delete -f cafe-virtual-server-2.yaml

	kubectl apply -f cafe-virtual-server-3.yaml

	curl -H "Host: cafe.example.com" https://192.168.0.3/tea --insecure -I
	200 OK

	curl -H "Host: cafe.example.com" https://192.168.0.3/coffee --insecure -I
	401 Unauthorized / Unauthenticated

	curl -H "Host: cafe.example.com" -H "apikey: mGcjH8Fv6U9y3BVF9H3Ypb9T" https://192.168.0.3/coffee --insecure -I
	200 OK

Header based Routing: Native


	kubectl apply -f cafe-virtual-server-4.yaml

	curl -H "Host: cafe.example.com" http://192.168.0.3/tea
	curl -H "Host: cafe.example.com" http://192.168.0.3

	curl -H "Host: cafe.example.com" -H "Drink: tea" http://192.168.0.3

	curl -H "Host: cafe.example.com" -H "Drink: coffee" http://192.168.0.3
