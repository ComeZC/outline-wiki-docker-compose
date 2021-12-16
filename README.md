# Docker compose for outline wiki
> Fork from repo:
> http://chsasank.github.io/outline-self-hosted-wiki.html

> Features:
> * A simple make and interactive bash script to help you generate all the conf required
> * A docker-compose to run your service
> * Dummy https certificate generator. Replace with your certificates after generation
> * Use minio instead of AWS S3, so that everything is really self-hosted
> * nginx reverse proxy for outline and minio

## Changes
* remove nginx and minio, use AWS ELB and S3
* remove slack for oauth, use Google Oauth

## Steps
1. Clone Repo
    ```
    git clone https://github.com/ComeZC/outline-wiki-docker-compose.git outline
    ```

2. Read the configuration file carefully: env.outline


3. Follow the instructions in the configuration file above, go to https://console.cloud.google.com/apis/credentials to configure Oauth Client
Dont forget add the right callback URL: https://<your Outline URL >/auth/google.callback

4. Follow the instructions in the above configuration file to create a new CLI-based AWS IAM user, grant sufficient S3 permissions, and you can get ID and Secret.

5. Follow the instructions in the configuration file above to create a S3 Bucket bucket and fill in its information in the configuration file, paying attention to the Region to which the bucket belongs

6. In the AWS admin panel, set the CORS policy of S3 Bucket bucket to ensure that there are no problems with cross-domain access.
    Configure Tutorial: https://docs.aws.amazon.com/AmazonS3/latest/userguide/enabling-cors-examples.html
    Here is the configure json(dont forget change the url to your right one)：
    ```
    [
        {
            "AllowedHeaders": [
                "*"
            ],
            "AllowedMethods": [
                "PUT",
                "POST"
            ],
            "AllowedOrigins": [
                "https://outline.XXXXX.com"
            ],
            "ExposeHeaders": []
        },
        {
            "AllowedHeaders": [],
            "AllowedMethods": [
                "GET"
            ],
            "AllowedOrigins": [
                "*"
            ],
            "ExposeHeaders": []
        }
    ]
    ```

7. Modify the external service port of the docker-compose.yml file, which is used for ELB destination pointing and EC2 Security Group Filter


8. In AWS admin panel，create ELB target group，and add Outline EC2 server and change to the correct port from step7

9. In AWS admin panel, add elb rule，redirect outline.XXXX.com to the target group created in step8

10. Run docker-compose, start outline service
    ```
    docker-compose up -d
    ```
11. Go to https://outline.XXXX.com，Enjoy yourself！