# portscanner.py
import socket  
import argparse  
from concurrent.futures import ThreadPoolExecutor  

def scan_port(target_ip: str, port: int) -> bool:  
    try:  
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:  
            s.settimeout(1)  
            return s.connect_ex((target_ip, port)) == 0  
    except (socket.error, ValueError):  
        return False  

def main():  
    parser = argparse.ArgumentParser(description="Python Port Scanner")  
    parser.add_argument("--target", required=True, help="Target IP")  
    parser.add_argument("--start", type=int, default=1, help="Start port")  
    parser.add_argument("--end", type=int, default=100, help="End port")  
    args = parser.parse_args()  

    print(f"Scanning {args.target} (Ports {args.start}-{args.end})...")  
    with ThreadPoolExecutor(max_workers=50) as executor:  
        ports = range(args.start, args.end + 1)  
        results = executor.map(lambda p: scan_port(args.target, p), ports)  
        for port, is_open in zip(ports, results):  
            if is_open:  
                print(f"Port {port}: OPEN")  

if __name__ == "__main__":  
    main()  
