

Overview
--------
This is a workshop meant to start building hands-on experience with AWS CloudFormation.  

The workshop proposes an initial, example CloudFormation template that describes 
an Amazon Simple Storage Service (S3) bucket, and then guides the user to create a
CloudFormation stack with that template: the stack will create the bucket on the user's behalf.  

The workshop will then guide the user on interactively adding configuration values to the template, 
and to apply changes to the stack.

Before getting started, make sure you go through the following
prerequisites:

- You will need an AWS account to run the workshop demo

- Familiarize with [AWS CloudFormation](https://aws.amazon.com/cloudformation/)

- Familiarize with the [CloudFormation User
  Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
  and keep it handy as a reference

- Familiarize with JSON and YAML [CloudFormation template
  formats](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-formats.html)

- Familiarize with the [template anatomy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html) structure

- Familiarize with the [Template Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html)and keep it handy in your bookmarks as you will need it when editing your templates


Let's get started!  Follow steps below:

1. You will need a working copy of the initial CloudFormation
   template: copy `example-s3-bucket-initial.template` into
   `example-s3-bucket.template`.  Please note that the
   `example-s3-bucket.template` file you will create is listed in the
   `.gitignore` file, that should be in the same directory of the
   initial template: the working copy of the template for this
   workshop will then be excluded from a `git` scope

2. The working copy of the initial example template, that you have
   made on the previous step, should only have an [S3
   bucket](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html)
   resource described with code, and no properties for the bucket are
   specified yet on the example template.  Take a look at the
   template, and familiarize with its structure and content: use the
   [CloudFormation template
   formats](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-formats.html)
   and the [template
   anatomy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html)
   pages in the AWS documentation as a reference.  When you are ready,
   go to the next step

3. [Log in to the CloudFormation
   Console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-login.html).
   This workshop uses the Console to create and update stacks: for a
   programmatic approach instead, you can use the [AWS Command Line
   Interface
   (CLI)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-cli.html)
   or an [AWS SDK](https://aws.amazon.com/getting-started/tools-sdks/)
   of your choice

4. [Create a stack with the
   Console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html).
   When [choosing a stack
   template](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-console-create-stack-template.html),
   you can choose between the `Amazon S3 URL` or the `Upload a
   template file` option.  First, familiarize with both options, and
   understand where and when you could use one versus the other.  For
   this workshop demo, you will be using the latter.  Next, initiate
   the stack creation process: choose to upload your working copy of
   the template and, when prompted to specify a stack name, input
   `my-example-s3-bucket`.  Follow directions in the console and then
   choose to create the stack when ready

5. In the CloudFormation Console, wait until the stack reaches the
   `CREATE_COMPLETE` status: the `Events` pane for the stack you are
   creating shows resource creation steps (in this case, your S3
   bucket)

6. You will now start adding properties to your bucket.  You will want
   the bucket name to follow this pattern:
   `my-example-bucket-AWS_REGION_NAME-AWS_ACCOUNT_ID-ENVIRONMENT_NAME`.
   The first two portions of the name can be derived automatically by
   using the `AWS::Region` and `AWS::AccountId` [Pseudo
   Parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html).
   The last portion, the environment name, will be an input parameter:
   this way, you could reuse the same template to predictibly create
   and maintain an S3 bucket with the same settings across all of your
   environments (such as Development, Quality Assurance, Production).
   Let's start with adding, to your working copy of the template, a
   main section called
   [Parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html),
   where you specify three predefined values, `dev`, `qa` and `prod`,
   and let's set `dev` as the default value (you can add it after the
   `Description` section: for more information, see [Template
   Anatomy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html)
   in the AWS documentation):

       Parameters:
         EnvironmentName:
           AllowedValues:
             - dev
             - qa
             - prod
           Default: dev
           Description: Specify a value for the environment name.
           Type: String

7. Next, let's go ahead and locate the block in the `Resources`
   section that starts with `S3Bucket`: this is the Logical ID of your
   bucket.  A Logical ID is used to reference resources in a template:
   for more information, see
   [Resources](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resources-section-structure.html)
   in the AWS documentation.  You will need to add the `Properties`
   section and specify the [property for the bucket
   name](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html#cfn-s3-bucket-name).
   To implement the pattern for the bucket name mentioned on the
   previous step, you will use
   [Fn::Sub](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-sub.html),
   that is an [intrinsic
   function](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resources-section-structure.html)
   you can use reference an element such as a parameter
   (`EnvironmentName`) and to compose a string at the same time.
   Please note you can also use the
   [Ref](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-ref.html)
   intrinsic function to reference e.g. a parameter on the same
   template, and in this case you are using `Fn::Sub` to also compose
   a string); for more information, see [Intrinsic Function
   Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html).
   Update your working copy of the template, for the `S3Bucket`
   resource block to look as follows (please note: the example below
   uses the `!Sub` short form as opposed to `Fn::Sub` - see the
   relevant documentation page for more details on this):

         S3Bucket:
           Type: AWS::S3::Bucket
           Properties:
             BucketName: !Sub 'my-example-bucket-${AWS::Region}-${AWS::AccountId}-${EnvironmentName}'

8. Select the stack you created, and choose `Update`.  Next, choose to
   replace the existing template, upload the new copy of the template
   and choose `Next`.  You will now have your first parameter on the
   next screen!  Choose `dev` (that should be the default selection)
   and follow steps until you get to the last screen where you create
   the stack: locate the `Change set preview` section on the bottom of
   the page, and wait until the `Changes` section populates: you
   should see a `Modify` action for the `S3Bucket` Logical ID (the
   logical name of the bucket you declared in the Resources section of
   the template), and a value of `true` for the `Replacement` column.
   Changes you have made (new bucket name in this case) require a
   replacement of the resource: since the existing bucket is empty,
   when you will choose to update the stack the operation should
   complete successfully (a new bucket is created and the previous one
   will be deleted - you cannot delete a bucket with objects in it).
   For more information on updates and properties, see [Update
   Behaviors of Stack
   Resources](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-update-behaviors.html)
   in the AWS documentation.  Choose to update the stack and observe
   changes in the `Events` pane on the Console, then when the stack is
   in the `UPDATE_COMPLETE` state, go to the next step

9. You will now configure the default encryption for your S3 bucket by
   specifying the [relevant
   property](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html#cfn-s3-bucket-bucketencryption)
   for your resource in your template.  With this demo, you will
   configure the Amazon S3-managed keys (SSE-S3) encryption.  Add the
   following snippet to the `Properties` section of your S3 bucket,
   then use the updated template to update your existing stack as you
   did before (when the stack update is complete, you can navigate to
   the S3 page in the Console, choose the bucket you created with
   CloudFormation and check changes you made and applied):

             BucketEncryption:
               ServerSideEncryptionConfiguration:
                 - ServerSideEncryptionByDefault:
                     SSEAlgorithm: AES256

10. On this step, you will enable the [property for the versioning
    configuration](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html#cfn-s3-bucket-versioning).
    Try this yourself this time, it's easy!  You will need to specify
    two lines:

      - the first line is the [name of the relevant
        property](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html#cfn-s3-bucket-versioning),

      - for the second line, you will need to look at [the relevant
        property for versioning
        configuration](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket-versioningconfig.html)

    Update your template with the new configuration, and then update
    the stack when ready!

11. Next, let's set up the [property for the object life cycle
    configuration](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html#cfn-s3-bucket-lifecycleconfig).
    When you configure life cycle of objects in your S3 bucket, you
    set up one or more `LifecycleConfiguration`
    [rules](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket-lifecycleconfig.html):
    with this demo, you will set up an example
    [rule](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket-lifecycleconfig-rule.html)
    such as:

      - Move all versioned objects (including non-current versions) in
        the bucket to [Amazon
        Glacier](https://aws.amazon.com/glacier/) after 7 days of
        creation, and

      - delete all objects older than a year

    Let's try this: update your template as follows, and then update
    the stack when ready!

              LifecycleConfiguration:
                Rules:
                  - ExpirationInDays: 365
                    Id: MoveToGlacierAfterSevenDaysAndDeleteAfterOneYear
                    NoncurrentVersionTransitions:
                      - StorageClass: GLACIER
                        TransitionInDays: 7
                    Status: Enabled
                    Transitions:
                      - StorageClass: GLACIER
                        TransitionInDays: 7

12. You will now go ahead and add
    [tags](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html#cfn-s3-bucket-tags)
    to your bucket: `BusinessUnitName` and `EnvironmentName`, whose
    values you want to be configurable as parameters.  You already
    have the `EnvironmentName` parameter: let's start with creating
    another parameter, `BusinessUnitName`, and add a few input
    validation controls to it (for more information, see
    [Parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html)
    in the AWS documentation):

          BusinessUnitName:
            AllowedPattern: ^[a-zA-Z0-9_-]*$
            ConstraintDescription: Please specify a valid value for the business unit.
            Description: 'Specify a value for the business unit: minimum length: 1 character,
              maximum: 32.  Allowed characters are alphanumeric characters, underscore and
              whitespace.'
            MaxLength: 32
            MinLength: 1
            Type: String

13. Let's continue to update the template and add the `Tags` property
    to bucket properties.  You will use the `Ref` intrinsic function
    this time to reference the `BusinessUnitName` and
    `EnvironmentName` input parameters; add the following example
    lines to your template, and update the stack with the updated
    template when ready:

              Tags:
                - Key: BusinessUnitName
                  Value: !Ref 'BusinessUnitName'
                - Key: EnvironmentName
                  Value: !Ref 'EnvironmentName'

14. You will now add a new resource: a [bucket
    policy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-policy.html),
    where you will add a statement to deny insecure connections (i.e.,
    non-https) to the bucket.  For more information on bucket and user
    policies for Amazon S3, see [Access Policy Language
    Overview](https://docs.aws.amazon.com/AmazonS3/latest/dev/access-policy-language-overview.html)
    in the AWS documentation.  The bucket policy will reference the bucket you
    created earlier in two places, by using:

    - `Ref` to attach the policy to the existing bucket, and

    - `Fn::Sub` as an example of composing the [Amazon Resource Name,
       (ARN)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)
       of the bucket that, in turn, you will concatenate to the `/*`
       string to complete a value you indicate for the `Resource`
       block

    Add the following example lines to your template, and update the
    stack with the updated template when ready:

          S3BucketPolicy:
            Type: AWS::S3::BucketPolicy
            Properties:
              Bucket: !Ref 'S3Bucket'
              PolicyDocument:
                Statement:
                  - Action: s3:*
                    Condition:
                      Bool:
                        aws:SecureTransport: 'false'
                    Effect: Deny
                    Principal: '*'
                    Resource: !Sub 'arn:aws:s3:::${S3Bucket}/*'
                    Sid: DenyInsecureConnections
                Version: '2012-10-17'

15. At the end of this workshop, terminate the stack to delete
    resources (bucket and bucket policy) you created with it
