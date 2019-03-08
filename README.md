# README #

Cherry Servers golang API for Cherry Servers RESTful API.

![](https://pbs.twimg.com/profile_images/900630217630285824/p46dA56X_400x400.jpg)

Installation
------------

```
go get github.com/cherryservers/cherrygo
```

### Examples ###

#### Get teams
You will need team ID for later calls, for example to get projects for specified team, you will need to provide team ID.
```go
c, err := cherrygo.NewClient()
if err != nil {
    log.Fatal(err)
}

teams, _, err := c.Teams.List()
if err != nil {
    log.Fatal("Error", err)
}

for _, t := range teams {

    fmt.Fprintf("%v\t%v\t%v\t%v\t%v\n",
        t.ID, t.Name, t.Credit.Promo.Remaining, t.Credit.Promo.Usage, t.Credit.Resources.Pricing.Price)
}
```

#### Get projects
After you have your team ID, you can list your projects. You will need your project ID to list your servers or order new ones.
```go
c, err := cherrygo.NewClient()
if err != nil {
    log.Fatal(err)
}

projects, _, err := c.Projects.List(teamID)
if err != nil {
    log.Fatal("Error", err)
}

for _, p := range projects {
    fmt.Fprintf(tw, "%v\t%v\t%v\n",
        p.ID, p.Name, p.Href)
}
```

#### Get plans
You know your project ID, so next thing in order to get new server is to choose one, we call it plans

```go
c, err := cherrygo.NewClient()
if err != nil {
    log.Fatal(err)
}

plans, _, err := c.Plans.List(projectID)
if err != nil {
    log.Fatalf("Plans error: %v", err)
}

for _, p := range plans {

    fmt.Fprintf(tw, "%v\t%v\t%v\t%v\n",
        p.ID, p.Name, p.Specs.Cpus.Name, p.Specs.Memory.Total)
}
```

#### Get images
After you manage to know desired plan, you need to get available images for that plan
```go
c, err := cherrygo.NewClient()
if err != nil {
    log.Fatal(err)
}

images, _, err := c.Images.List(planID)
if err != nil {
    log.Fatal("Error", err)
}

for _, i := range images {
    fmt.Fprintf(tw, "%v\t%v\t%v\n",
        i.ID, i.Name, i.Pricing.Price)
}
```

#### Order new server
Now you are ready to order new server
```go
c, err := cherrygo.NewClient()
if err != nil {
    log.Fatal(err)
}

addServerRequest := cherrygo.CreateServer{
    ProjectID:   projectID,
    Hostname:    hostname,
    Image:       image,
    Region:      region,
    SSHKeys:     sshKeys,
    IPAddresses: ipaddresses,
    PlanID:      planID,
}

server, _, err := c.Server.Create(projectID, &addServerRequest)
if err != nil {
    log.Fatal("Error while creating new server: ", err)
}
```