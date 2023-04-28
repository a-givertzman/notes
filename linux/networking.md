# Linux Networking

## Config

- configuration file path:  
`/etc/network/interfaces`

- view all network interfaces:  
`ip a`

## netcat

- install netcat ubuntu/debian:  
`sudo apt install netcat`

### Port scanning

- in general use command:  
`netcat -z -v domain.com 1-1000`  
`netcat -z -n -v 198.51.100.0 1-1000`  
`-z` option to perform a scan instead of attempting to initiate a connection  
`-v` option to tell netcat to provide more verbose information  
`-n` option to specify that you do not need to resolve the IP address using DNS

- redirect standard error to standard output using the 2>&1 bash syntax, then filter the results with grep:  
`netcat -z -n -v 198.51.100.0 1-1000 2>&1 | grep succeeded`

### TCP Transfering

- listen on port 4444:  
`netcat -l 4444`  
`netcat domain.com 4444`

- view loggged in realtime:  
`tail -f ./esp32.log`

- send raw file:  
`netcat -l 4444 > received_file` - receaving a file  
`netcat domain.com 4444 < original_file` - sending a file

- sending zipped files/folders:  
`netcat -l 4444 | tar xzvf -` - unzip receaved files to the current dir  
`tar -czf - * | netcat domain.com 4444` - compress all files in current dir and send  

### UDP Transfering

- listening:  
`nc -l -u -k 2000 > ./esp32.log`

- sending general:  
`netcat -u host port`  
`netcat host startport-endport`

### Simple Web Server

- listen once on port 8080  
`sudo printf 'HTTP/1.1 200 OK\n\n%s' "$(cat index.html)" | netcat -l 8080`

- listen ifinit on port 8080:  
`while true; do printf 'HTTP/1.1 200 OK\n\n%s' "$(cat index.html)" | netcat -l 8888; done`

try following html for fun:
