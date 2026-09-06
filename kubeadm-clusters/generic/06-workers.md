# Boot the worker nodes

Here we will join the worker nodes to the cluster. You will need the `kubeadm join` command from the previous step

Ref: https://stackoverflow.com/questions/59629319/unable-to-upgrade-connection-pod-does-not-exist

## Join Workers

If you did not note down the join command on the controlplane node after running `kubeadm`, you can recover it by running the following on `controlplane`

```bash
kubeadm token create --print-join-command
```

[//]: # (host:node01-node02)
[//]: # (comment:Run kubeadm join)

On each of `node01` and `node02` do the following

1.  Become root (if you are not already)

    ```
    sudo -i
    ```

1.  Join the node

    > Paste the `kubeadm join` command output by `kubeadm init` on the control plane

### Verify

On `controlplane` run the following. After a few seconds both nodes should be ready

```
kubectl get nodes
```

[Kubernetes Documentation Link](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/adding-linux-nodes/)


Next: [Test](./07-test.md)</br>
Prev: [Boot controlplane](./05-controlplane.md)

ubuntu@kmaster-node:~$ kubectl get pods -o wide
NAME                               READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
nginx-deployment-589dfdf74-2nbdz   1/1     Running   0          2m14s   10.244.1.6   worker-node1   <none>           <none>
nginx-deployment-589dfdf74-dlxbm   1/1     Running   0          2m14s   10.244.2.7   worker-node2   <none>           <none>
nginx-deployment-589dfdf74-szfwf   1/1     Running   0          2m14s   10.244.1.7   worker-node1   <none>           <none>

## Pod deployment

Clean up your old standalone pod so it doesn't cause name or label confusion (if any):
1)
kubectl delete pod nginx-pod

2)
kubectl apply -f nginx-deployment.yaml

### Method 1: Create a NodePort Service to access multiple pods created using pod deployment feature.

This is the cleanest approach because it maps a single port across all your physical cluster nodes. You can hit any node's physical IP address, and Kubernetes will route your request to one of the 3 running Nginx pods.

1.Expose the deployment:Run on master node.Create a NodePort service targeting your new deployment:

Bash
kubectl expose deployment nginx-deployment --type=NodePort --port=80 --name=nginx-service

2.Get the dynamic port number:Find the mapped port.Check the service details to find the high port assigned by Kubernetes:

Bash
kubectl get svc nginx-service

Look at the PORT(S) column. It will display something like 80:3XXXX/TCP (for example, 80:32451/TCP). That five-digit number is your entry port.

3.Access the page:Open Mac browser.Open your Mac browser and point it to the physical IP of your master node or any worker node using that port:
Yeah. End to end. Alexa, stop. Alexa shops. Alexa, stop. Alexa, stop. Going on, I lost a color board. Dou H. Hey, hey. Hey, Cortana. Hey, Cortana. Hey. The. Hey, Cortana. Hey, Cortana. Hey, Cortana. Hey, Cortana. No, I didn't believe we brought a college on it again. Hey, Guru. Alexa, what's the time now? The time is 7:33 AM. Zinta integration. User. Hey Joy, how's it doing? I. Coming after move. II remembered it was a animal. I mean food type drawing. Plant Shelter Annual. Hmm. What if I told you one bullet could finish your entire Battle Pass? Let me explain. Everyone knows breaking builds gives XP, but nobody wants to spend 30 minutes building a massive base just to destroy it and barely gain half a level. But this method lets you finish your entire Battle Pass with just one bullet. And no, this isn't another fake XP coin scam with a timer. You don't have to grind Battle Royale for 10 hours a day, and you don't need to spend a single V buck. All you need to do is load into this map, head to the teleporter when you spawn and enter the code 12314. And once you're in the room, shoot out this ramp, reset it, and keep farming XP until you finish the Battle Pass. If I told you one bullet could finish your entire Battle Pass, let me explain. This **** sweats like 9:00. Ready. **** **** **** Here's how to get the Cube Grim Sprite in Fortnite Epic Games out of the Cube variant to eight different sprites, including the Grim. By searching regular and rare chess around the map, you can find cube sprites. However, if you're looking for the Cube Grim, you should use the Sprite Locator. Once unlocked from the Sprite Mastery track activated to scan the nearby area and mark a buried Sprite chest. Digging it up guarantees a special variant like Gold Gummy Galaxy. Oil or cube Sprite chest also have a higher chance for rare Sprite. I mean, that's how it's frankly done as it's time. It's happening, guys. It's happening right now. I just want you to take it in right now, everybody. The grim cheap Sprite is the rarest key released in Fort