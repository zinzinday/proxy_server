# Proxy Server Setup and Usage Guide

## Overview

This project is a SOCKS4, SOCKS5, and HTTP proxy server implemented in Go. It allows multiple listener addresses and
supports username/password authentication.

## Prerequisites

To build this project, you will need:

- Go programming language installed (version 1.16 or later).
- A terminal or command prompt for executing commands.

## Files in the Project

1. **proxy_server**: The executable file for running the proxy server.
2. **config.yaml**: The configuration file for defining listener addresses and user credentials.
3. **proxy_server.service**: The systemd service file for configuring the proxy server as a Linux service.

## Configuration File (`config.yaml`)

The `config.yaml` file is used to configure the proxy server. It includes the listener addresses (IP and port
combinations) and the authentication credentials.

Here is an example of the `config.yaml` file:

```yaml
listeners:
  - "127.0.0.1:21139"
  - "0.0.0.0:21140"

users:
  - username: "abc"
    password: "pw123"
  - username: "user2"
    password: "password2"
```

### Configuration Details

- **listeners**: A list of IP addresses and ports that the proxy server will listen on.
- **users**: A list of username/password pairs that are allowed to use the proxy server.

## Building the Project

To build the project, you need to have Go installed. Run the following command to build the executable:

```sh
go build -o proxy_server main.go
```

This will create an executable file named `proxy_server` in the current directory.

## Running the Proxy Server on Linux

Once you have the `proxy_server` file and the `config.yaml` file, you can run the proxy server by executing the
following command in your terminal:

```sh
./proxy_server
```

Note: After building the executable, you do not need to have Go installed on the system where you are running the proxy
server.

The server will read from the `config.yaml` file and start listening on the specified addresses.

### Setting Up as a Systemd Service

To run the proxy server as a systemd service on Linux, follow these steps:

1. **Create the Service File**

   Create a file named `proxy_server.service` with the following content:

   ```ini
   [Unit]
   Description=Proxy Server Service
   After=network.target

   [Service]
   Type=simple
   User=nobody
   WorkingDirectory=/path/to/your/proxy_server_directory
   ExecStart=/path/to/your/proxy_server_directory/proxy_server
   Restart=on-failure

   [Install]
   WantedBy=multi-user.target
   ```

   Replace `/path/to/your/proxy_server_directory` with the actual path to the directory containing your `proxy_server`
   executable.

2. **Copy the Service File**

   Copy the `proxy_server.service` file to the systemd directory:

   ```sh
   sudo cp proxy_server.service /etc/systemd/system/
   ```

3. **Reload Systemd**

   Reload systemd to recognize the new service:

   ```sh
   sudo systemctl daemon-reload
   ```

4. **Start the Service**

   Start the proxy server service:

   ```sh
   sudo systemctl start proxy_server
   ```

5. **Enable the Service at Boot**

   To ensure the service starts automatically at boot, enable it with the following command:

   ```sh
   sudo systemctl enable proxy_server
   ```

## Running the Proxy Server on Windows

If you are on Windows, you can build the project to create an `.exe` file:

```sh
go build -o proxy_server.exe main.go
```

Note: After building the executable, you do not need to have Go installed on the system where you are running the proxy
server.

Then, run the proxy server:

```sh
proxy_server.exe
```

Or if you are already in the directory containing the executable, use:

```sh
./proxy_server.exe
```

The server will read from the `config.yaml` file and start listening on the specified addresses.

## Usage Example

To use the proxy server with `curl`, you can run the following command to send a request through the proxy:

```sh
curl -x socks5://abc:pw123@127.0.0.1:21139 http://example.com
```

### Explanation

- **-x socks5://abc:pw123@127.0.0.1:21139**: This specifies the proxy to use. The username (`abc`) and password (
  `pw123`) are provided along with the address of the proxy server.

## Troubleshooting

1. **Failed to Start Listener**: If the proxy server fails to start, ensure that the IP address and port are not already
   in use.
2. **Authentication Issues**: Make sure the username and password provided in the request match the credentials defined
   in `config.yaml`.
3. **No Such Host Error**: Ensure that the hostname you are trying to connect to is valid and that your DNS settings are
   properly configured.

## Notes

- Ensure that the `config.yaml` file is in the same directory as `proxy_server` or `proxy_server.exe` when running the
  executable.
- You can modify the `config.yaml` file to add or remove listeners and user credentials as needed.

