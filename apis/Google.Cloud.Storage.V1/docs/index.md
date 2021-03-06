{{title}}

{{description}}
It wraps the `Google.Apis.Storage.v1` generated library, providing a higher-level API to make it easier to use.

{{version}}

{{installation}}

{{auth}}

# Getting started

Common operations are exposed via the
[StorageClient](obj/api/Google.Cloud.Storage.V1.StorageClient.yml) class.

# Client life-cycle management

In many cases you don't need to worry about disposing of
`StorageClient` objects, and can create them reasonably freely -
but be aware that this *can* causes issues with memory and network
connection usage. We advise you to reuse a single client object if
possible; if your architecture requires you to frequently create new
client objects, please dispose of them to help with timely resource
clean-up. See [the resource clean-up guide](../guides/cleanup.html#rest-based-apis) for more
details.

# Sample code

[!code-cs[](obj/snippets/Google.Cloud.Storage.V1.StorageClient.txt#Overview)]

## Signed URLs

Signed URLs can be created to provide limited access to specific buckets and
objects to anyone in possession of the URL, regardless of whether they have
a Google account.

For example, Signed URLs can be created to provide read-only access to
existing objects:

[!code-cs[](obj/snippets/Google.Cloud.Storage.V1.UrlSigner.txt#SignedURLGet)]

Or write-only access to put specific object content into a bucket:

[!code-cs[](obj/snippets/Google.Cloud.Storage.V1.UrlSigner.txt#SignedURLPut)]

### Signing URLs without a service account credential file

If you need to sign URLs but don't have a full service account
credential file (with private keys) available, you can create a
`UrlSigner.IBlobSigner` implementation to perform the signing part.
The most common implementation of this is likely to be to use the
IAM service to perform the signing, with the
[Google.Apis.Iam.v1](https://www.nuget.org/packages/Google.Apis.Iam.v1/)
package. Here's a sample implementation:

[!code-cs[](obj/snippets/Google.Cloud.Storage.V1.UrlSigner.txt#IamServiceBlobSigner)]

(We may make this available in its own package at some point in the
future.)

To make use of this, the account making the request needs the
`iam.serviceAccounts.signBlob` permission, which is usually granted
via the "Service Account Token Creator" role.

Here's an example showing how you could use this to sign a
URL on behalf of the default Compute Engine credential on an
instance. (This example will only work when running on Google Cloud
Platform, as it relies on information from the metadata server.) If
you want to use a different service account, you could include the
account ID as part of your application configuration.

[!code-cs[](obj/snippets/Google.Cloud.Storage.V1.UrlSigner.txt#IamServiceBlobSignerUsage)]

## Upload URIs

In some cases, it may not make sense for client applications to have permissions
to begin an upload for an object, but an authenticated service may choose to grant
this ability for individual uploads. Signed URLs are one option for this. Another
option is for the service to start a resumable upload session, but instead of
performing the upload, sending the resulting upload URI to the client application
so it can perform the upload instead. Unlike sessions initiated with a signed URL,
a pre-initated upload session will force the client application to upload through
the region in which the session began, which will likely be close to the service,
and not necessarily the client.

[!code-cs[](obj/snippets/Google.Cloud.Storage.V1.StorageClient.txt#UploadObjectWithSessionUri)]

## Customer-supplied encryption keys

Storage objects are always stored encrypted, but if you wish to
specify your own encryption key instead of using the server-supplied
one, you can do so either for all operations with a particular
`StorageClient` or on individual ones.

[!code-cs[](obj/snippets/Google.Cloud.Storage.V1.StorageClient.txt#CustomerSuppliedEncryptionKeys)]

## Change notification via Google Cloud Pub/Sub

You can configure a bucket to send a change notification to a
[Google Cloud Pub/Sub](https://cloud.google.com/pubsub/) topic
when changes occur. The sample below shows how to create a Pub/Sub
topic, set its permissions so that the change notifications can be
published to it, and then create the notification configuration on a
bucket. You'll need to add a dependency on the
`Google.Cloud.PubSub.V1` NuGet package to create the topic and
manage its permissions.

[!code-cs[](obj/snippets/Google.Cloud.Storage.V1.StorageClient.txt#NotificationsOverview)]
