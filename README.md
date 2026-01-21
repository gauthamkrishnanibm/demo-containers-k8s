# The Technical Lab: Building "Mario's Pizza API"

## Phase 1: The Recipe (The Dockerfile)

First, we define what goes into our container. We’ll use a simple Nginx web server to represent our pizza shop.

- Create a folder named marios-pizza.

- Create a file inside named index.html and paste:

```
<html>
  <body style="background-color: #ffcccc; text-align: center; font-family: sans-serif;">
    <h1>Welcome to Mario's Digital Pizza! 🍕</h1>
    <p>Status: <b>Oven is Hot</b> | Ingredients: <b>Freshly Packaged</b></p>
  </body>
</html>
```

- Create a file named Dockerfile (no extension) and paste:

```
# Use the 'Kitchen' (Base Image)
FROM nginx:alpine
# Add our 'Recipe' (HTML file) to the oven
COPY index.html /usr/share/nginx/html/index.html
# Open the 'Serving Window'
EXPOSE 80
```

## Phase 2: Boxing the Pizza (Podman)

Now we use Podman to build the image. This is the "Standardized Box" that will run the same way on any machine.

- Build the Image:

```
podman build -t marios-pizza:v1 .
```

- Test the Box (Local Run):

```
podman run -d -p 8080:80 --name pizza-test marios-pizza:v1
```

Open http://localhost:8080 to see your shop online!

## Phase 3: Hiring the Master Chef (Kubernetes via Rancher)

One shop is easy, but what if Mario wants a Cluster of shops that never go down? We give the box to Kubernetes.

- Create the Manifest: Create a file named pizza-delivery.yaml:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pizza-deployment
spec:
  replicas: 3 # Mario wants 3 ovens running at all times!
  selector:
    matchLabels:
      app: pizza
  template:
    metadata:
      labels:
        app: pizza
    spec:
      containers:
      - name: pizza-container
        image: marios-pizza:v1
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: pizza-service
spec:
  type: LoadBalancer # The 'Front Counter' for customers
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30001
  selector:
    app: pizza
```

- Deploy to Rancher Desktop:

```
kubectl apply -f pizza-delivery.yaml
```

- Observe "Self-Healing"

```
kubectl get pods
```

you will see 3 "Pizza Shops" running.

- The Magic Trick: Delete one pod:

```
kubectl delete pod <pod-name>
```
Watch as Kubernetes (the Master Chef) immediately notices and starts a new one to keep the count at 3.

## Phase 4: Scaling for a "Pizza Rush"

If the shop gets too busy, Mario doesn't need to panic. He just tells the Chef to scale.
Run this command:
```
kubectl scale deployment pizza-deployment --replicas=10
```
Instantly, you have 10 ovens running across your cluster.

## The OpenShift Perspective (The Enterprise Upgrade)

In a real enterprise, you wouldn't just run these commands manually. You would use OpenShift's Web Console.

- Source-to-Image (S2I): Instead of writing a Dockerfile, you just point OpenShift to your GitHub, and it builds the box for you.
- Routes: Instead of complicated LoadBalancers, OpenShift gives you a simple URL like ```pizza.apps.company.com``` automatically.



