# Running Jenkins on Docker

## Step 1: Start Jenkins Container

```bash
docker run -d \
  --name cicd-jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -p 8000:8000 \
  -p 8001:8001 \
  -p 8002:8002 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v jenkins_home:/var/jenkins_home \
  lotfinejad/cicd-workshop:latest
```

---

## Step 2: Fix Docker Permissions (CRITICAL!)

```bash
docker exec -it -u root cicd-jenkins chmod 666 /var/run/docker.sock
docker exec -it -u root cicd-jenkins mkdir -p /root/.docker
docker exec -it -u root cicd-jenkins sh -c 'echo "{}" > /root/.docker/config.json'
```

---

## Step 3: Wait for Jenkins to Start

```bash
# Watch logs
docker logs -f cicd-jenkins
```

**Wait for this line:**
```
Jenkins is fully up and running
```

**Ignore these warnings (they're harmless):**
- ⚠️ `Failed Loading plugin Dark Theme` - Optional theme
- ⚠️ `AdminWhitelistRule` - Deprecated setting
- ⚠️ `unhealthy: dark-theme` - Same theme issue

**Press Ctrl+C to exit logs**

---

## Step 4: Access Jenkins

1. Open browser: **http://localhost:8080**
2. Login: 
   - Username: `admin`
   - Password: `admin123`

---

## Step 5: Verify Docker Works

```bash
docker exec -it cicd-jenkins docker ps
```

**Should show running containers without errors**

---

## Success Checklist

- [ ] Jenkins UI accessible at http://localhost:8080
- [ ] Can login with admin/admin123
- [ ] `docker ps` works inside container
- [ ] No permission errors

---

## Useful Commands

```bash
# Stop Jenkins
docker stop cicd-jenkins

# Start Jenkins
docker start cicd-jenkins

# View logs
docker logs -f cicd-jenkins

# Restart Jenkins
docker restart cicd-jenkins

# Remove everything and start fresh
docker rm -f cicd-jenkins
docker volume rm jenkins_home
# Then run Step 1 again
```
