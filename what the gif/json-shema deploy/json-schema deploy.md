# deploying json schemas to azure blob storage

Welcome back to this serias about json schemas.
We have learned how to define a schema, validations, and how to structure your json
no we are going to learn about the deployment.

when doing this, im going to use azure blob storage, and I'm assuming you already have this created. i will try to include the specific options you need to make this work. if you have any issues please contact me, and i will try to include this in this document.

## strategies

when it comes to actually deploying the schemas, you need to think about the following:

* where to host them
* version names
* uploading
* custom domain (if you want)

im not going to talk about hosting, as the only thing you relly need is for the files to be avalible directly from the interwebs without some form of auth token or extra verification (like sas key on blob storage).
!!! warning about auth
    generally all the files of the schema needs to be avalible for anyone to look at without any auth. there are ways of coding your app to handle auth as some "json schema" handlers have the possibility to handle auth, but unless the schema hosts info you dont want to share, I wouldn't mind it.

## Json schema versioning

if you have consumed json schemas from web before (think azure resource management schema) you would notice that all of them have some form of versioning in the 'schema path'. for example, lets take a look at the scehma for azure arm: `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`

as you see in the above url, the version for this specific schema is `2019-04-01`. there are 

Now you have several strategies on how to handle schema version, but probably the easiest is to use some form of `yyyy-mm-dd` date you uloaded/built the schema. you could use "standard" versioning (`v1`,`v2`), but the issue here is that unless you have some form of map or config that say  , but eh

## uploading

Im going to assume you are going to use git or some form for code control that enables automatic deployments on commits or releases (devops pipelines or github actions).

generally for working with cloud storage I highly suggest you take a look at rclone. its probably the best software for copying from one location to another. its free, lightweight, open source and supports a TON of storage solutions.

### devops

in order to be able to use rclone on devops pipeline, you first need to download rclone

!!! info devops extension
    there is a extension in devops store named 'rclone', however i cannot find any project site and cannot read any of the code contained within the extension so i cannot in good faith recomend it.
