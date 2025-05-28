# 변수 정의
DOCKER_REGISTRY = camel.uangel.com:5000
IMAGE_NAME = helloworld
TAG = latest
SERVER_DOCKERFILE = examples/helloworld/greeter_server/Dockerfile
CLIENT_DOCKERFILE = examples/helloworld/greeter_client/Dockerfile
BUILD_DIR = ../../

# 기본 목표
.PHONY: all docker_build docker_build_server docker_build_client docker_tag docker_push docker_clean docker_deploy docker_create_network docker_remove_network docker_run_server docker_run_client kubectl_run_server kubectl_run_client kubectl_delete_server kubectl_delete_client

# 전체 빌드를 위한 기본 목표
all: docker_build

# 서버 이미지 빌드
docker_build_server:
	cd $(BUILD_DIR) && docker buildx build --platform linux/amd64,linux/arm64 -t $(DOCKER_REGISTRY)/$(IMAGE_NAME)_server:$(TAG) -f $(SERVER_DOCKERFILE) .

# 클라이언트 이미지 빌드
docker_build_client:
	cd $(BUILD_DIR) && docker buildx build --platform linux/amd64,linux/arm64 -t $(DOCKER_REGISTRY)/$(IMAGE_NAME)_client:$(TAG) -f $(CLIENT_DOCKERFILE) .

# 서버 및 클라이언트 이미지 병렬 빌드
docker_build:
	$(MAKE) docker_build_server & $(MAKE) docker_build_client & wait



# 태그 추가
docker_tag:
	docker tag $(DOCKER_REGISTRY)/$(IMAGE_NAME)_server:$(TAG) $(DOCKER_REGISTRY)/$(IMAGE_NAME)_server:$(TAG)-$(shell date +%Y%m%d%H%M)
	docker tag $(DOCKER_REGISTRY)/$(IMAGE_NAME)_client:$(TAG) $(DOCKER_REGISTRY)/$(IMAGE_NAME)_client:$(TAG)-$(shell date +%Y%m%d%H%M)

# 이미지를 Docker 레지스트리에 푸시
docker_push:
	docker push $(DOCKER_REGISTRY)/$(IMAGE_NAME)_server:$(TAG)
	docker push $(DOCKER_REGISTRY)/$(IMAGE_NAME)_client:$(TAG)

# 빌드된 이미지를 청소
docker_clean:
	docker rmi $(DOCKER_REGISTRY)/$(IMAGE_NAME)_server:$(TAG)
	docker rmi $(DOCKER_REGISTRY)/$(IMAGE_NAME)_client:$(TAG)

# 모든 이미지를 빌드하고 푸시
docker_deploy: docker_build docker_tag docker_push

docker_create_network:
	docker network create $(IMAGE_NAME)_network
docker_remove_network:
	docker network rm $(IMAGE_NAME)_network
docker_run_server:
	docker run -it --rm --net $(IMAGE_NAME)_network --name $(IMAGE_NAME)_server -p 50051:50051 $(DOCKER_REGISTRY)/$(IMAGE_NAME)_server:$(TAG)
docker_run_client:
	docker run -it --rm --net $(IMAGE_NAME)_network --name $(IMAGE_NAME)_client $(DOCKER_REGISTRY)/$(IMAGE_NAME)_client:$(TAG) --addr=$(IMAGE_NAME)_server:50051

kubectl_run_server:
	kubectl apply -f greeter_server/server.yaml
kubectl_run_client:
	kubectl apply -f greeter_client/client.yaml
kubectl_delete_server:
	kubectl delete -f greeter_server/server.yaml
kubectl_delete_client:
	kubectl delete -f greeter_client/client.yaml