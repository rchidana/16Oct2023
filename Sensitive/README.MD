### Adding some sensitive information in this folder and checking if security scans can sniff them out

Potential sensitive information from Git Repositories can be scanned using free/open source tools such as https://github.com/trufflesecurity/trufflehog <br>

This tool can be installed in many ways (for more details, please refer to https://github.com/trufflesecurity/trufflehog) and the easiest option is to run this within a docker container as follows: <br>

```
docker run -it -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --repo https://github.com/rchidana/20Feb2022
```
