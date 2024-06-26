# syntax=docker/dockerfile:1.4
FROM golang:1.18-alpine AS builder

WORKDIR /code

ENV CGO_ENABLED 0
ENV GOPATH /go
ENV GOCACHE /go-build

COPY go.mod go.sum ./
RUN --mount=type=cache,target=/go/pkg/mod/cache \
    go mod download

COPY . .

RUN --mount=type=cache,target=/go/pkg/mod/cache \
    --mount=type=cache,target=/go-build \
    go build -o bin/backend main.go

CMD ["/code/bin/backend"]

FROM builder as dev-envs

RUN apk update &&
apk add git &&

RUN addgroup -S docker &&
adduser -S --shell /bin/bash --ingroup docker vscode

# install Docker tools (cli, buildx, compose)
COPY --from=gloursdocker/docker / /

CMD ["go", "run", "main.go"]

FROM scratch
COPY --from=builder /code/bin/backend /usr/local/bin/backend
CMD ["/usr/local/bin/backend"]
