ARG GOLANG_VERSION=1.15
FROM golang:${GOLANG_VERSION} as builder
ENV VERSION=v1.2.8

WORKDIR /build
COPY go.mod .
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -a -gcflags=-trimpath=$(go env GOPATH) -asmflags=-trimpath=$(go env GOPATH) -ldflags '-X github.com/wunderio/csi-rclone/pkg/rclone.DriverVersion=$(VERSION) -extldflags "-static"' -o _output/csi-rclone-plugin ./cmd/csi-rclone-plugin


FROM alpine:3.13
RUN apk add --no-cache ca-certificates bash fuse curl unzip

RUN curl https://rclone.org/install.sh | bash

COPY --from=builder /build/_output/csi-rclone-plugin /bin/csi-rclone-plugin
ENTRYPOINT ["/bin/csi-rclone-plugin"]
