# Sample project to verify headers sent from openapi handler of graphql-mesh

It looks like that as soon as mesh receives the first request it saves variables and headers for reuse in subsequent calls to the backing api, without reading parameters from following request.

to try the sample, open 3 terminals

- `npm run echo` for http-echo-server backing api
- `npm run serve` for mesh server
- the third one is needed to execute curls ex: `bash curl1.sh`

the expected (wrong) result is

```none
--- on open terminal execute

bash curl1.sh

--- on mesh console

http Call GET http://localhost:3000/thing/one?
  http headers: {"content-type":"application/json","accept":"application/json","x-from-variables":"fromVariable1","x-from-request":"from-headers-1"}

--- on echo console

--> GET /thing/one HTTP/1.1
--> content-type: application/json
--> accept: application/json
--> x-from-variables: fromVariable1
--> x-from-request: from-headers-1

--- on open terminal execute

bash curl2.sh

--- on mesh console expected

  http headers: {"content-type":"application/json","accept":"application/json","x-from-variables":"fromVariable2","x-from-request":"from-headers-2"}

--- on mesh console actual

  http headers: {"content-type":"application/json","accept":"application/json","x-from-variables":"fromVariable1","x-from-request":"from-headers-1"}

--- on echo console expected

--> GET /thing/one HTTP/1.1
--> content-type: application/json
--> accept: application/json
--> x-from-variables: fromVariable2
--> x-from-request: from-headers-2

--- on echo console actual

--> GET /thing/one HTTP/1.1
--> content-type: application/json
--> accept: application/json
--> x-from-variables: fromVariable1
--> x-from-request: from-headers-1

```

other variables behave as expected.
