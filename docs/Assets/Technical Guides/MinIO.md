# Using AIDA Object Storage (MinIO)

This guide explains how to access you newly created data bucket hosted on the AIDA Object Storage (MinIO) server.

## Pre-requisites

To follow this guide, you need:

- a basic understanding of [what an S3 Bucket is](https://www.techtarget.com/searchaws/definition/AWS-bucket).
- have access to a pair of access and secret keys (either read-only, or read/write) to connect to a bucket.
<a class="forge-button">ask for access keys (for a new/existing bucket)</a>

> **_Note:_** When requesting access to a new bucket, you will obtain a .json-file from the AIDA team that has the following format:
>
> ```json
> {
>  "url": "https://ask-for-the-url/api/v1/service-account-credentials",
>  "accessKey": "foo",
>  "secretKey": "bar",
>  "api": "s3v4",
>  "path": "auto"
> }
> ```
>
> Make sure to have it around as you follow this guide as you will need the `accessKey` and `secretKey` to connect to your bucket.

## How to connect to MinIO using CLI

You can use the [Minio Client (mc)](https://min.io/docs/minio/linux/reference/minio-mc.html#minio-client) command line tool to interact with your data bucket(s) from your terminal.

> _The MinIO Client mc command line tool provides a modern alternative to UNIX commands like ls, cat, cp, mirror, and diff with support for both filesystems and Amazon S3-compatible cloud storage services._

Follow [the official documentation](https://min.io/docs/minio/linux/reference/minio-mc.html#quickstart) for instructions on how to install the tool on your computer.

Use the command bellow to configure your connection to your data bucket:

```bash
mc alias set <ALIAS_NAME> https://ask-for-the-url <BUCKET_ACCESS_KEY> <BUCKET_SECRET_KEY>
```

> **_Note:_** Be sure to update all `<VALUES>` before applying the command !

Then to test your connection, you can execute the following command:

```bash
mc ls <ALIAS_NAME>
```

This should show the list of your bucket in the output.

Then run the following command to browse the content of your bucket:

```bash
mc ls <ALIAS_NAME>/<BUCKET_NAME>
```

> **_Note:_** If there is nothing yet in the bucket, the command will not return anything

Refer to the [MinIO Client documentation](https://min.io/docs/minio/linux/reference/minio-mc.html#minio-client).

## Access to MinIO using Pandas

The following snippets of code documents some basic interactions with an data bucket using pandas library.

```python
import pandas as pd

MINIO_ENDPOINT = 'https://ask-for-the-url'
BUCKET_NAME = os.environ['BUCKET_NAME']

# environment key variables should be defined locally
minio_storage={
    "key": os.environ['MINIO_KEY'],
    "secret": os.environ['MINIO_SECRET'],
    "client_kwargs": {"endpoint_url": MINIO_ENDPOINT}
}

# creating a file on s3 server
def create_file_from_df(df, minio_storage, bucket_name, path_to_file):
    try:       
        df.to_csv("s3://"+ bucket_name + "/" + path_to_file, index=False, sep=';', quotechar='"', quoting=1,  storage_options=minio_storage, encoding='utf_8')
    except Exception as e:
        print(f'Error creating file in bucket on S3 server: {e}')
        raise

#retrieve a .csv file from the s3 server
def retrieving_csv_from_s3(minio_storage, bucket_name, path_to_file):
    try:
        df = pd.read_csv("s3://"+ bucket_name + "/" + path_to_file, storage_options=minio_storage)
    except Exception as e:
        df= None
        print(f"Error while retrieving location CSV file from S3: {e}")

    return  df
```

## How to connect to MinIO using Iceberg with a notebook
