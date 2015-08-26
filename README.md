# BSEC 2015 Conference Materials

This repository contains the pptx slides and IPython Notebook file used to demonstrate basic large-scale image analysis using thunder-python.

If you want to run the IPython notebook, you'll need to install a few prerequisites, as well as perform some setup.

## Install Prerequisites

 * python 2.7+
 * [jupyter 4.0+](https://jupyter.org/)
 * [Spark 1.4+](http://d3kbcqa49mib13.cloudfront.net/spark-1.4.1-bin-hadoop1.tgz) (built against **Hadoop 1.X**)
 * [thunder-python 0.5+](http://thunder-project.org/)
 * [awscli](https://aws.amazon.com/cli/)
 
## Download the data

[We used the FERET dataset](http://www.itl.nist.gov/iad/humanid/feret/feret_master.html), pulling out all the .tif files into a single directory; we did not make use of any ground-truth information.

Once the data is downloaded, the next step is to put the data on S3.

## Upload to Amazon S3

We use the AWS CLI tool installed earlier to streamline the process of interacting with S3. These operations can easily be performed using the AWS browser-based dashboard, if that is your preference.

 1. Create an S3 bucket.

 `aws s3 mb s3://mybucket --region us-east-1`

 Important to note: there are some issues that can arise if the region specified in S3 differs from the region in which your EC2 instances (next part) are spun up. For this reason, it is always good to specify the region rather than let AWS try to infer it.

 2. Copy the local image data to the S3 bucket.

 `aws s3 cp path/to/image/folder s3://mybucket/path --recursive

If you want to cut down on the verbosity of the `cp` command, you can add the `--only-show-errors` flag, and it will complete silently unless an error is encountered.

## Allocate EC2 instances

You can follow the instructions in [thunder-project's documentation](http://thunder-project.org/thunder/docs/install_ec2.html); in particular, make sure you've set up your AWS access key. The critical portions of the setup process are reproduced here.

 1. Launch a cluster. For BSEC2015, we used a 15-instance cluster.

 `thunder-ec2 -k mykey -i ~/mykey.pem -s 15 --copy-aws-credentials launch bsec2015`

 The option `--copy-aws-credentials` automatically copies your AWS keys to the cluster as it is being set up. This allows your cluster automatic access to S3. If you don't use this option, you'll need to copy the keys manually once the cluster is running, otherwise your cluster won't be able to access the images you just uploaded to S3. This step will take awhile.

 2. Follow the instructions in the thunder-project documentation for [using the IPython notebook](http://thunder-project.org/thunder/docs/install_ec2.html#use-the-ipython-notebook); specifically, "Method 1: Connect directly over SSL." This involves configuring your EC2 cluster master to accept incoming HTTPS connections.

## Run the notebook

Once your IPython server is running, you can open up the IPython notebook file stored in this repository, configure it for your setup, and you're good to go!

**One last note**: When we ran this notebook using 15 AWS EC2 instances (m3.2xlarge) on the full FERET dataset (~90,000 images), it took *roughly 50 minutes* to run to completion.
