# 1. Move to your user's home directory (where you have full write access)
cd ~

# 2. Download the script again
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

# 3. Make it executable
chmod 700 get_helm.sh

# 4. Run it with sudo to let it install the binary to /usr/local/bin
sudo ./get_helm.sh

helm repo add headlamp https://kubernetes-sigs.github.io/headlamp/
helm repo update
helm install my-headlamp headlamp/headlamp --namespace kube-system

# 1. Grab the exact name of the Headlamp pod and save it to a variable
export POD_NAME=$(kubectl get pods --namespace kube-system -l "app.kubernetes.io/name=headlamp,app.kubernetes.io/instance=my-headlamp" -o jsonpath="{.items[0].metadata.name}")

# 2. Grab the port the container is listening on (usually 80 or 8080)
export CONTAINER_PORT=$(kubectl get pod --namespace kube-system $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")

# 3. Print the access URL message
echo "Visit http://127.0.0.1:7777 to use your application"

# 4. Start the port-forward tunnel
kubectl --namespace kube-system port-forward $POD_NAME 7777:$CONTAINER_PORT

kubectl --namespace kube-system port-forward --address 0.0.0.0 $POD_NAME 7777:$CONTAINER_PORT

Get the token using
  kubectl create token my-headlamp --namespace kube-system

http://<your-kmaster-node-ip>:7777

### To open a new terminal for the master node, follow the either of the steps:

Method 1: Open a New Window on your Mac (Easiest)
Since you are on a MacBook Air, you can simply spin up a second connection side-by-side:

Click on your active terminal window on your Mac and press Cmd + N (to open a new window) or Cmd + T (to open a new tab).

In that brand new local terminal window, SSH back into your master node just like you did the first time:

Bash
ssh ubuntu@192.168.4.28
(If you use a private key file, remember to add -i /path/to/key.pem)

Method 2: Use screen or tmux (Directly inside your current window)
If your current terminal is "stuck" running the kubectl port-forward command and you don't want to open a new app window, you can use a terminal multiplexer. Most Ubuntu server setups have screen installed by default.

Press Ctrl + C on your master node to temporarily stop your port-forward command.

Start a screen session:

Bash
screen
Run your port-forward command inside this screen session:

Bash
kubectl --namespace kube-system port-forward --address 0.0.0.0 $POD_NAME 7777:$CONTAINER_PORT
Now, press Ctrl + A, then tap D on your keyboard.

This "detaches" the process, sending it into the background while keeping it alive. You are now instantly returned to a clean, usable command prompt on your master node without opening any new windows!

### To kill a process

# 1. Find the PID running on port 7777
sudo lsof -i :7777

# 2. Kill the process (replace <PID> with the number from the command above)
sudo kill -9 <PID>

deyJhbGciOiJSUzI1NiIsImtpZCI6IkxRQ2tUTjgxQzVrX1htZU9nRF9TX1JoYlJyRGJMOHh5WG1vd3FoQ0dfRkUifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzg0NTEwNTMyLCJpYXQiOjE3ODQ1MDY5MzIsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwianRpIjoiM2E3MGE0NmEtMjljZC00NjI4LTk3OTktYzlmZTAwOGNkOWU4Iiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJteS1oZWFkbGFtcCIsInVpZCI6IjViMDZlNjliLWY3YjktNDIxOS1iOWZhLWQ2MjQzNmEwMjAxOCJ9fSwibmJmIjoxNzg0NTA2OTMyLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06bXktaGVhZGxhbXAifQ.cKxGMW0geLwkMM6YK04BsBDMslSZawQOzpRDwW8vvCRVXokMf3HWhopeRfexfIN68k3RRSxodewDgXKzbECWhenWLzLFOy1R6W-DnNgdCFY0OHNANJHRrfko1B00K-8APui8InNaXApqEpZKWMkxkLZDnjLGAQfGmgErLbEHSh2sv5_YiXNX0foyUc6y6NpYbzRVqTDiQssMqaAM1gu99pNSvugYFNJgsHuMWN9Ja4viPWu2Gzr7wHKzU3Zd3mzT3OwUTnm5XxHY8bxoPz14a-uEGR95aEyN2KyQj1XTQ4zGQhrhbwQQPHnbhOf-0jSeSMMTE6uiihNyJSW0qTbsJA