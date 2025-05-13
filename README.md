Go HTTP/1.1 and HTTP/2 Server

This Go server handles both HTTP/1.1 and HTTP/2 requests on different ports, using a JSON configuration file for routing and response handling. It also tracks metrics for each route.

Features

	1. Supports both HTTP/1.1 and HTTP/2 on separate ports.

	2. JSON-based configuration for defining routes, HTTP methods, status codes, headers, and response bodies.

	3. Real-time metrics tracking and reporting, including transaction per second (TPS) for each route.

	4. Optional logging to a specified file.

	5. Added counter for http2 request received count.


Prerequisites
	Go 1.17+ installed on your machine.

Installation
	Clone the repository:

	git clone https://at.mavenir.com/bb/scm/~gmr/go-http-server.git
	cd go-http-server
	Build the server:

	go build -o httpServer


Configuration
	The server uses a JSON configuration file to define routes. An example configuration (config.json) might look like this:

	json
	{
		"routes": [
			{
				"path": "/hello1$",
				"method": "GET",
				"status": 200,
				"delay": 1000,
				"includeRspBodyfromReqBody": false,  
				"body": {"message": "Hello, World!"},
				"headers": {
					"X-Custom-Header": "MyValue"
				}
			},
			{
				"path": "/hello1$",
				"method": "POST",
				"status": 200,
				"delay": 1000,
				"includeRspBodyfromReqBody": false,  
				"body": {"message": "Hello, World!"},
				"headers": {
					"X-Custom-Header": "MyValue"
				}
			},			{
				"path": "/hello2$",
				"method": "PUT",
				"status": 200,
				"delay": 0,
				"includeRspBodyfromReqBody": true,  
				"headers": {
					"X-Custom-Header": "MyValue"
				}
			}

		]
	}


Configuration Options:

	path: The route path (supports regex).
	method: The HTTP method (e.g., GET, POST).
	status: The HTTP status code to return.
	delay: Delay in responding to Request by milliSeconds.
	includeRspBodyfromReqBody: If true, the response body will be the same as the request body.
	body: The response JSON body to return (if includeRspBodyfromReqBody is false).
	headers: A map of headers to include in the response.


Usage

	Command-line Options
	-config: Path to the configuration file (default: config.json).
	-ip: IP address to listen on (default: 0.0.0.0).
	-ports: Comma-separated list of ports to listen on for HTTP/1.1 and HTTP2")
	-enableLogging: Enable logging to file (default: false).
	-cpuprof: Enable CPU profiling (default: false).
	-log_path: Path to log file (default: /tmp/server.log).


	Running the Server
		./httpServer -config=config.json -ports=8080,3001 -log=true -log_path=/var/tmp/server.log

	Example Request
		curl -X GET http://localhost:8080/api/v1/hello1
		curl -X PUT http://localhost:8080/api/v1/hello2

	Sample httpServer output

		---------------------------------------------------------------------------------------
		Metrics Report:  2024-08-09 12:02:01
		Listening on IP: 0.0.0.0, Ports: 8080,3001
		---------------------------------------------------------------------------------------
		API                                                          Count           TPS        
		=======================================================================================
		GET ---> /hello1$                                          	23976           100.00       
		POST ---> /hello1$                                    		10000           100.00       
		PUT ---> /hello2$                                    		23976           100.00       
		=======================================================================================
		Total Received                                              57952            0.00       
		Unknown API                                                  0               0.00       
		=======================================================================================

	Logging
	If logging is enabled, the server will write logs to the specified file. Logs include incoming requests, errors, and response details.

	Metrics
	The server tracks metrics such as request counts and TPS for each route. Metrics are printed to the console every second.

	Graceful Shutdown
	The server listens for an interrupt signal (Ctrl+C) and shuts down both HTTP/1.1 and HTTP/2 servers gracefully.


