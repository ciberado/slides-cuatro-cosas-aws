# S3

## Preparation

Start a `tmux` session, configure you region, and create your own bucket:

```bash
tmux

export AWS_DEFAULT_REGION=us-east-1
export BUCKET_NAME=s3workshop$RANDOM
echo Your bucket name is going to be $BUCKET_NAME. Take note of it.
aws s3 mb s3://$BUCKET_NAME
```

Get the required data to test the upload performance and start a new pane
with `htop`:

```bash
wget https://github.com/ciberado/workshops/raw/main/s3-the-lesser-known-features/assets/covers.zip
mkdir data
unzip covers.zip  01*.jpg 02*.jpg -d covers/
cd covers
ls . | wc -l
```
```bash
tmux splitw -t "$session_uuid:" -d "htop"
```

## The lack of Glob

S3 operations doesn't support typical Linux `*` for working with files: only
prefixes are directly implemented in the path argument. Additionally, object
keys are not hierarchical: the `/` is considered just like any other character.
But it influences the response provided by the AWS CLI, as you will see:

```bash
# export BUCKET_NAME=s3workshop32501

aws s3 ls s3://$BUCKET_NAME/covers-simple     # (ok, but not what you intended)
aws s3 ls s3://$BUCKET_NAME/covers-simple/    # (ok)
aws s3 ls s3://$BUCKET_NAME/covers-simple/01* # (what?)
aws s3 ls s3://$BUCKET_NAME/covers-simple/01*.jpg # (double what?)
aws s3 ls s3://$BUCKET_NAME/covers-simple/01  # (ok, surprisingly)
```

### How pagination works

Check how the most basic list query works: it returns a number of items (max 1000),
and a pagination token.

```bash
aws s3api list-objects-v2 \
  --bucket $BUCKET_NAME \
  --prefix covers-simple/ \
  --max-items 3 \
  --output table
```

Save the token to be able to get the next bunch of files:

```bash
TOKEN=$(aws s3api list-objects-v2 \
  --bucket $BUCKET_NAME \
  --prefix covers-simple/ \
  --max-items 3 \
  | tee first-page.json \
  | jq .NextToken -r)
echo Next token is $TOKEN.
cat first-page.json
cat first-page.json | jq ".Contents[].Key"
```

Use the saved token to continue with the process of getting all the
names:

```bash
token=$(aws s3api list-objects-v2 \
  --bucket $BUCKET_NAME \
  --prefix covers-simple/ \
  --max-items 3 \
  --starting-token $TOKEN \
  | tee second-page.json \
  | jq .NextToken -r)
cat second-page.json
cat second-page.json | jq ".Contents[].Key"
```

## Improving ingestion

Test how much time does it take to upload those files. Note that `s3 cp`
will launch a thread for each processor in the VM:

```bash
time aws s3 cp --recursive . s3://$BUCKET_NAME/covers-simple/ > /dev/null
```

Test now how does it takes if we divide all files in groups (using
the first three letters of the name as the prefix) and then
upload two files in parallel, trying to saturate the compute capacity:

```bash
time ls . \
  | cut -c1-3 \
  | uniq \
  | xargs -P 6 -I '{}' \
      sh -c 'aws s3 cp --recursive \
          --exclude "*" --include "{}*" \
          . s3://$BUCKET_NAME/covers-parallel/ > /dev/null' 
```

Optionally, you can close the additional pane:

```bash
tmux kill-pane -a -t "$session_uuid:"
```

