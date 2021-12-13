### copilot init
```bash
$ copilot app init --domain domain.com
$ copilot init
# Application name: app
# Workload type: Load Balanced Web Service
# Service name: api
# Dockerfile: ./Dockerfile
# parse EXPOSE: no EXPOSE statements in Dockerfile ./Dockerfile
# Port: 80
# deploy a staging environment?: No
```
### copilot env init
```bash
$ copilot env init --name staging --profile profilename \
--import-vpc-id vpc-xxxxxx \
--import-public-subnets subnet-xxxx,subnet-yyyyy \
--import-private-subnets subnet-zzzzz,subnet-ooooo
# ✔ Linked account[create complete]  [70.9s]
```
### copilot svc deploy
Sevice作成
```bash
$ copilot svc deploy --name api --env staging
# [create complete]  [317.0s]
```
### copilot storage init
Storage(addons)作成
```bash
$ copilot storage init \
  -n app-cluster -t Aurora -w api --engine PostgreSQL \
  --initial-db app
# ✔ Wrote CloudFormation template for Database Cluster app-cluster at copilot/api/addons/app-cluster.yml
# Recommended follow-up actions:
#   - Update api's code to leverage the injected environment variable appCLUSTER_SECRET.
# For example, in JavaScript you can write `const {username, host, dbname, password, port} = JSON.parse(process.env.appCLUSTER_SECRET)`.
#   - Run `copilot deploy --name api` to deploy your storage resources.
```
## Update api's code to leverage the injected environment variable appCLUSTER_SECRET.
- `addons/app-cluster.yml`
```diff
    Properties:
      DBSubnetGroupDescription: Group of Copilot private subnets for Aurora cluster.
      SubnetIds:
-        !Split [',', { 'Fn::ImportValue': !Sub '${App}-${Env}-PrivateSubnets' }]
+        - subnet-xx
+        - subnet-xx
```
- deploy
```bash
$ copilot deploy --name api --env staging
# [update complete]  [1208.6s]
```

## copilot : pipeline

```bash
$ copilot pipeline init \
--url https://github.com/name/app.git \
--environments "staging" 
```
```bash
$ git add
$ git commit -m "add copilot file"
$ git push -u origin main
```
pipelineをawsにあげる
```bash
$ copilot pipeline update
```

## copilot : delete
```bash
$ copilot app delete
✔ Successfully added pipeline resources to your application: app

ACTION REQUIRED! Go to https://console.aws.amazon.com/codesuite/settings/connections to update the status of connection copilot-galap-app-controller-ap from PENDING to AVAILABLE.
⠼ Creating a new pipeline: pipeline-app
```