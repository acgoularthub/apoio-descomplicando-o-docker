FROM gcc:4.9 AS buildando

WORKDIR /app
ADD . /app
RUN g++ exemplo.cpp -o a

FROM alpine
WORKDIR /app
COPY --from=buildando /app/a /app/
ENTRYPOINT ./a
