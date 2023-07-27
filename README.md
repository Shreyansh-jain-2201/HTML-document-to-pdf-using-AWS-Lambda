# HTML document to pdf using AWS Lambda

This GitHub repository houses a serverless application that allows seamless conversion of HTML documents into PDF files using AWS Lambda. The application leverages AWS's serverless architecture, enabling automatic scaling and cost-effectiveness for HTML to PDF conversion.

*Simply ``POST`` an HTML document to a web service and receive an application/pdf document in response.*

## Implementation

* The [Serverless](https://www.serverless.com/) framework is utilized to establish a web service on the AWS cloud by leveraging [AWS API Gateway](https://aws.amazon.com/api-gateway/) and [AWS Lambda](https://aws.amazon.com/lambda/).
* [Puppeteer](https://github.com/puppeteer/puppeteer) and [Chromium](https://github.com/alixaxel/chrome-aws-lambda) are employed by the web service to transform an HTML document into a PDF file.

## Documentation

### Overview

This software deploys a web service using AWS infrastructure with endpoints that convert HTML documents into PDF documents.

### Requirements

- Create an [Amazon Web Services](https://aws.amazon.com/) account — Sign up [here](https://portal.aws.amazon.com/billing/signup#/start/email).
- Install the command line tools for your AWS account — [instructions](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html).
- Install [Node.js](https://nodejs.org/en) — it is recommended to use the [Node Version Manager](https://github.com/nvm-sh/nvm).
- Install the [Yarn](https://yarnpkg.com/) package management tool — [instructions](https://classic.yarnpkg.com/en/docs/install#windows-stable)
- Install the Serverless framework — [instructions](https://www.serverless.com/framework/docs/getting-started/)

### Technical Overview

- A web application is created using the [AWS API Gateway](https://aws.amazon.com/api-gateway/) service.
- Any HTTP requests received by the web application are handled by an [AWS Lambda Function](https://aws.amazon.com/lambda/).
- The Lambda Function loads [Puppeteer](https://github.com/puppeteer/puppeteer) and [Chromium](https://github.com/alixaxel/chrome-aws-lambda) from an [AWS Lambda Layer](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html).
- The Lambda Function uses Puppeteer and Chromium to convert an HTML document into a PDF file.

### Deployment

From the project's root directory, install the required dependencies.

```shell
yarn install
```

Then, execute the deployment, as follows:

```
yarn deploy:prod
```

First, the deployment process will execute a ``make`` command, building the Lambda Layer package (which houses Puppeteer and Chromium). Then, it will configure AWS services (e.g. API Gateway, Lambda, etc). Obviously, your AWS account must have the sufficient access to perform these operations.

### Endpoints
Your web service exposes one endpoint:

- ```/print``` - Renders the PDF document using Puppeteer (and Chromium).

The endpoint expects a JSON document, with the following properties:

- ```html``` - A required String which contains the HTML document itself.
- ```source``` - An optional String describing the document.
- ```settings``` - An optional Object which contains [puppeteer options](https://github.com/puppeteer/puppeteer/blob/v5.2.1/docs/api.md#pagepdfoptions).

Here is an example:
```bash
{
    "source": "testing",
    "html": "<html><body><h1>Hello World</h1><div>You have converted an HTML document into a PDF file.</div></body></html>"
}
```

### Testing
Before deployment, you can run the code locally - simulating the AWS infrastructure, as follows:

```shell
cd ./packages/api

sls invoke local -f print --path print/test/print.json
sls invoke local -f print --path print/test/print.5MB.json
```
After deployment, you can test the web service by issuing a ```POST``` request to the actual web service. Here is an example cURL command (obviously, you'll need to correct the hostname and stage name).

```shell
curl 'https://{api-identifier}.execute-api.us-east-1.amazonaws.com/{stage|prod}/print' \
  -X 'POST' \
  --output testing.pdf \
  --data-binary '{"source":"testing","html":"<html><body><h1>Hello World</h1><div>You have converted an HTML document into a PDF file.</div></body></html>"}'
```
### Limitations

- The size of the HTML document cannot exceed 10 MB (a limit of API Gateway service).
  
- The processing time required to generate the PDF file cannot exceed 30 seconds (a limit of API Gateway service).

In theory, these limits can be overcome. However, additional engineering would be required. At present, there is no plan to make these enhancements.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
