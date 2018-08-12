# emr-airflow

Repo containing Amazon EMR and Apache Airflow related code

## Apache Airflow

[Apache Airflow](https://airflow.apache.org)

Airflow 1.9

Requires:

* Gunicorn 19.4
  * Had to update from 19.3 to 19.4 using `pip install gunicorn==19.41`

### Validating a DAG

A DAG can be initially valiated using the airflow `list_tasks` command:

```{bash}
airflow list_tasks s3tohdfs --tree
```

### Connecting to Hive

Make sure the hive integration is installed using the following command:

```{bash}
sudo pip install airflow[hive]
```

If the error `ImportError: cannot import name CSRFProtect` is received in ensure Flask-WTF is at least 0.14:

```{bash}
pip install Flask-WTF==0.14
```

## S3 to HDFS on EMR

Amazon's s3-dist-cp tool, which comes installed on EMR can be used to quickly copy/move files to or from S3 and HDFS.

```{bash}
s3-dist-cp --src s3://dwdii/putevt --dest /user/hadoop/temp --srcPattern .*\.ZIP
```

 The EMR_EC2_DefaultRole was the security context. Presumably, this would be whatever the EMR cluster was setup with. Permission to the bucket and KMS key (in IAM, if S3 bucket encyption is enabled) are needed inorder for a successful copy from S3 to HDFS.

### Troubleshooting

 Look for logs in HDFS if the standard output of s3-dist-cp isn't providing enough info. For example, if the error is "Reducer task failed to copy 1 files", query `/var/log/hadoop-yarn/apps/hadoop/logs` for the logs associated with the s3-dist-cp execution.

 ```{bash}
 hadoop fs -ls /var/log/hadoop-yarn/apps/hadoop/logs
 ```

 See also, the following StackOverflow articles:

 [https://stackoverflow.com/questions/48901249/s3distcp-on-local-hadoop-cluster-not-working](https://stackoverflow.com/questions/48901249/s3distcp-on-local-hadoop-cluster-not-working)

 [https://stackoverflow.com/questions/51041257/copying-files-from-hdfs-to-s3-on-emr-cluster-using-s3distcp](https://stackoverflow.com/questions/51041257/copying-files-from-hdfs-to-s3-on-emr-cluster-using-s3distcp)
