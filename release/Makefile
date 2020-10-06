## This Makefile is used to publish skopeo container images with Travis CI ##
## Environment variables, used in this Makefile are specified in .travis.yaml

export DOCKER_CLI_EXPERIMENTAL=enabled
GOARCH ?= $(shell go env GOARCH)

# Build container image of skopeo upstream based on host architecture
build-image/upstream:
	docker build -t "${UPSTREAM_IMAGE_REPO}-${GOARCH}" contrib/skopeoimage/upstream

# Build container image of skopeo stable based on host architecture
build-image/stable:
	docker build -t "${STABLE_IMAGE_REPO}-${GOARCH}" contrib/skopeoimage/stable

# Push container image of skopeo upstream (based on host architecture) to image repository
push-image/upstream:
	echo "${QUAY_PASSWORD}" | docker login quay.io -u "${QUAY_USERNAME}" --password-stdin
	docker push "${UPSTREAM_IMAGE_REPO}-${GOARCH}"

# Push container image of skopeo stable (based on host architecture) to image default and extra repositories
push-image/stable:
	echo "${QUAY_PASSWORD}" | docker login quay.io -u "${QUAY_USERNAME}" --password-stdin
	docker push "${STABLE_IMAGE_REPO}-${GOARCH}"
	docker tag "${STABLE_IMAGE_REPO}-${GOARCH}" "${EXTRA_STABLE_IMAGE_REPO}-${GOARCH}"
	docker push "${EXTRA_STABLE_IMAGE_REPO}-${GOARCH}"

# Create and push multiarch image manifest of skopeo upstream
push-manifest-multiarch/upstream:
	echo "${QUAY_PASSWORD}" | docker login quay.io -u "${QUAY_USERNAME}" --password-stdin
	docker manifest create "${UPSTREAM_IMAGE_REPO}" $(foreach arch,${MULTIARCH_MANIFEST_ARCHITECTURES}, ${UPSTREAM_IMAGE_REPO}-${arch})
	docker manifest push --purge "${UPSTREAM_IMAGE_REPO}"

# Create and push multiarch image manifest of skopeo stable
push-manifest-multiarch/stable:
	echo "${QUAY_PASSWORD}" | docker login quay.io -u "${QUAY_USERNAME}" --password-stdin
	docker manifest create "${STABLE_IMAGE_REPO}" $(foreach arch,${MULTIARCH_MANIFEST_ARCHITECTURES}, ${STABLE_IMAGE_REPO}-${arch})
	docker manifest push --purge "${STABLE_IMAGE_REPO}"
	# Push to extra repository
	docker manifest create "${EXTRA_STABLE_IMAGE_REPO}" $(foreach arch,${MULTIARCH_MANIFEST_ARCHITECTURES}, ${EXTRA_STABLE_IMAGE_REPO}-${arch})
	docker manifest push --purge "${EXTRA_STABLE_IMAGE_REPO}"
