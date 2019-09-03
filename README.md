## Introduction

A set of tools to simplify the usage of AWS

### Tools

 - `aws-mfa`: A tool to manage MFA session creation, allowing the settings to be saved to a
              configuration file and providing a set of commands to run to utilise the
              created session.
 - `aws-s3-tree`: A tool to query S3 buckets in a user-friendly way.

 - `aws-policy-minimize`: A tool to find a minimal AWS policy that will allow some actions.

## Getting started

Install the dependencies:

```
pip3 install -r requirements.txt
```

Then run the commands directly from the `bin/` directory.



#### aws-policy-minimize

   `aws-policy-minimize [options] <testscript> <maxperm.json> <policyARN> -o <minperm.json>`


This tool allows you to minimize an AWS policy.

You need to supply `maxperm.json` containing a policy to minimize. aws-policy-minimize will
subtract permissions until it finds a minimal subset.

To use:
  * You need to first set up a policy attached to the things you want to test. This WILL BE OVERWRITTEN.
  * You also need a json file containing a policy that's known to work (IE, one that is very generous).
    This must enumerate everything explicitly, not use wildcards.
  * You also need a test script, which tries all the things you want the policy to allow. It must yield
    a return code of zero if it succeeeds, otherwise any other code. (DD libraries also allow  a third
    'invalid coniguration' code, but since we should aloways generate a syntactically correct policy, 
     we don't use that).
  * If you need to use different AWS credentials for the test to the ones needed for uploading the
    policy, place these in AWS variables starting TEST_, eg TEST_AWS_ACCESS_KEY_ID.

Limitations:
  

  Currently assumes the use of environment variables to pass in the AWS credentials, not any other mechanism.

  Relies on a timeout after uploading  the policy. This seems to need to be 30secs, which
    makes the process a bit slow. As this is based on trial and error, it may turn out that
    it is not enough.  If the results of the test are therefore inconsistent, we get an assertion
    failure from picire, which isn't very useful in producing a good error message. Indeed,
    error messages are not great yet anywhere.

  Always sets the policy version to be the default, so don't use it on a live policy that
    is being used for other things.

  The library (picire) we use for minimization seens to have a bug in that it sometimes restests
   a configuration which it already tried (it's supposed to cache answers, but apparently doesn't)

  It doesn't know about '*' so you need to specify all the individual actions/resources.

