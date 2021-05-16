# kic-demo-1
KIC Demo - Rate Limiting, Access Control List, API Key Authentication &amp; Header Based Routing

Pre-req:

Have a working K8s Environment. ( I used Docker Desktop installed on my local macintosh laptop )
Have an Ingress Controller image built and available for Helm to pull from ( I created an nginx-plus-ingress image on my local machine, did not push it to a cloud based repository )

Install KIC via HELM:

helm install my-release nginx-stable/nginx-ingress --set controller.image.repository=nginx-plus-ingress --set controller.nginxplus=true --set controller.enableSnippets=true --set controller.enablePreviewPolicies=true


Deploy the Demo, Tea/Coffee Application:

	kubectl apply -f cafe.yaml
	kubectl apply -f cafe-secret.yaml
	kubectl apply -f cafe-virtual-server.yaml
	# Replace the IP Address in the command below with the one for your KIC Installation
	curl -H "Host: cafe.example.com" https://192.168.0.3/tea --insecure



Rate Limiting: Via Policy

	kubectl delete -f cafe-virtual-server.yaml

	kubectl apply -f cafe-virtual-server-1.yaml

	kubectl apply -f rate-limit.yaml
	# Replace the IP Address in the command below with the one for your KIC Installation
	curl -H "Host: cafe.example.com" https://192.168.0.3/tea --insecure

	You should get 503 - Service Temporarily Unavailable message


Access Control List: Via Policy

	kubectl delete -f cafe-virtual-server-1.yaml

	kubectl apply -f cafe-virtual-server-2.yaml

	kubectl apply -f access-control-policy-allow.yaml

	kubectl apply -f access-control-policy-deny.yaml
	# Replace the IP Address in the command below with the one for your KIC Installation
	curl -H "Host: cafe.example.com" https://192.168.0.3/coffee --insecure

	You should get 403 - Forbidden Error

API Key Authentication: Via Snippets

	kubectl delete -f cafe-virtual-server-2.yaml

	kubectl apply -f cafe-virtual-server-3.yaml
	# Replace the IP Address in the command below with the one for your KIC Installation
	curl -H "Host: cafe.example.com" https://192.168.0.3/tea --insecure -I
	200 OK
	# Replace the IP Address in the command below with the one for your KIC Installation
	curl -H "Host: cafe.example.com" https://192.168.0.3/coffee --insecure -I
	401 Unauthorized / Unauthenticated
	# Replace the IP Address in the command below with the one for your KIC Installation
	curl -H "Host: cafe.example.com" -H "apikey: mGcjH8Fv6U9y3BVF9H3Ypb9T" https://192.168.0.3/coffee --insecure -I
	200 OK

Header based Routing: Native


	kubectl apply -f cafe-virtual-server-4.yaml
	# Replace the IP Address in the command below with the one for your KIC Installation
	curl -H "Host: cafe.example.com" http://192.168.0.3/tea
	curl -H "Host: cafe.example.com" http://192.168.0.3
	# Replace the IP Address in the command below with the one for your KIC Installation
	curl -H "Host: cafe.example.com" -H "Drink: tea" http://192.168.0.3
	# Replace the IP Address in the command below with the one for your KIC Installation
	curl -H "Host: cafe.example.com" -H "Drink: coffee" http://192.168.0.3
