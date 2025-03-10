---
title: Modify the Program | Azure
h1: Modify the Program
linktitle: Modify the Program
meta_desc: This page provides an overview on how to update an Azure project from a Pulumi program.
weight: 6
menu:
  getstarted:
    parent: azure
    identifier: azure-modify-program

aliases: ["/docs/quickstart/azure/modify-program/"]
---

Now that your S3 bucket is provisioned, let's add an object to it. First, from within your project directory, create a new `index.html` file with some content in it.

{{< chooser os "macos,linux,windows" / >}}

{{% choosable os macos %}}

```bash
cat <<EOT > index.html
<html>
    <body>
        <h1>Hello, Pulumi!</h1>
    </body>
</html>
EOT
```

{{% /choosable %}}

{{% choosable os linux %}}

```bash
cat <<EOT > index.html
<html>
    <body>
        <h1>Hello, Pulumi!</h1>
    </body>
</html>
EOT
```

{{% /choosable %}}

{{% choosable os windows %}}

```powershell
@"
<html>
  <body>
    <h1>Hello, Pulumi!</h1>
  </body>
</html>
"@ | Out-File -FilePath index.html
```

{{% /choosable %}}

Now that you have your new `index.html` with some content, you can enable static website support, upload `index.html` to a storage container, and retrieve a public URL through the use of resource properties. These properties can be used to define dependencies between related resources or to retrieve property values for further processing.

{{< chooser language "typescript,python,go,csharp" / >}}

{{% choosable language typescript %}}

To start, open `index.ts` and add the following right after the storage account creation:

```typescript
// Enable static website support
const staticWebsite = new storage.StorageAccountStaticWebsite("staticWebsite", {
    accountName: storageAccount.name,
    resourceGroupName: resourceGroup.name,
    indexDocument: "index.html",
});
```

{{% /choosable %}}
{{% choosable language python %}}

To start, open `__main__.py` and add the following right after the storage account creation:

```python
# Enable static website support
static_website = storage.StorageAccountStaticWebsite("staticWebsite",
    account_name=account.name,
    resource_group_name=resource_group.name,
    index_document="index.html")
```

{{% /choosable %}}
{{% choosable language go %}}

To start, open `main.go` and add the following right after the storage account creation:

```go
// Enable static website support
staticWebsite, err := storage.NewStorageAccountStaticWebsite(ctx, "staticWebsite", &storage.StorageAccountStaticWebsiteArgs{
    AccountName:       account.Name,
    ResourceGroupName: resourceGroup.Name,
    IndexDocument:     pulumi.String("index.html"),
})
if err != nil {
    return err
}
```

{{% /choosable %}}
{{% choosable language csharp %}}

To start, open `MyStack.cs` and add the following right after the storage account creation:

```csharp
// Enable static website support
var staticWebsite = new StorageAccountStaticWebsite("staticWebsite", new StorageAccountStaticWebsiteArgs
{
    AccountName = storageAccount.Name,
    ResourceGroupName = resourceGroup.Name,
    IndexDocument = "index.html",
});
```

{{% /choosable %}}

The static website resource leverages the storage account and resource group names defined previously in your program.

Now use all of these cloud resources, and a local `FileAsset` resource, to upload `index.html` into your storage container.

{{% choosable language typescript %}}

```typescript
// Upload the file
const indexHtml = new storage.Blob("index.html", {
    resourceGroupName: resourceGroup.name,
    accountName: storageAccount.name,
    containerName: staticWebsite.containerName,
    source: new pulumi.asset.FileAsset("index.html"),
    contentType: "text/html",
});
```

{{% /choosable %}}

{{% choosable language python %}}

```python
# Upload the file
index_html = storage.Blob("index.html",
    resource_group_name=resource_group.name,
    account_name=account.name,
    container_name=static_website.container_name,
    source=pulumi.FileAsset("index.html"),
    content_type="text/html")
```

{{% /choosable %}}
{{% choosable language go %}}

```go
// Upload the file
_, err = storage.NewBlob(ctx, "index.html", &storage.BlobArgs{
    ResourceGroupName: resourceGroup.Name,
    AccountName:       account.Name,
    ContainerName:     staticWebsite.ContainerName,
    Source:            pulumi.NewFileAsset("index.html"),
    ContentType:       pulumi.String("text/html"),
})
if err != nil {
    return err
}
```

{{% /choosable %}}
{{% choosable language csharp %}}

```csharp
// Upload the file
var index_html = new Blob("index.html", new BlobArgs
{
    ResourceGroupName = resourceGroup.Name,
    AccountName = storageAccount.Name,
    ContainerName = staticWebsite.ContainerName,
    Source = new FileAsset("index.html"),
    ContentType = "text/html",
});
```

{{% /choosable %}}

{{% choosable language typescript %}}

Finally, at the end of `index.ts`, export the resulting storage container's endpoint URL to stdout for easy access:

```typescript
// Web endpoint to the website
export const staticEndpoint = storageAccount.primaryEndpoints.web;
```

{{% /choosable %}}

{{% choosable language python %}}

Finally, at the end of `__main__.py`, export the resulting storage container's endpoint URL to stdout for easy access:

```python
# Web endpoint to the website
pulumi.export("staticEndpoint", account.primary_endpoints.web)
```

{{% /choosable %}}

{{% choosable language go %}}

Finally, at the end of `main.go`, export the resulting storage container's endpoint URL to stdout for easy access:

```go
// Web endpoint to the website
ctx.Export("staticEndpoint", account.PrimaryEndpoints.Web())
```

{{% /choosable %}}

{{% choosable language csharp %}}

Finally, at the end of `MyStack.cs`, export the resulting storage container's endpoint URL to stdout for easy access:

```csharp
// Web endpoint to the website
this.StaticEndpoint = storageAccount.PrimaryEndpoints.Apply(
        primaryEndpoints => primaryEndpoints.Web);
```

```csharp
[Output]
public Output<string> StaticEndpoint { get; set; }
```

{{% /choosable %}}

Now that you have declared how you want your resources to be provisioned, it is time to deploy these remaining changes.

{{< get-started-stepper >}}
