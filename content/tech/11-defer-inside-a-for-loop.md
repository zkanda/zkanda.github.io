+++
author = "Zakatell Kanda"
categories = ["technology", "golang"]
date = 2017-08-02T10:17:25Z
description = ""
draft = false
slug = "defer-inside-a-for-loop"
tags = ["technology", "golang"]
title = "golang: defer inside a for loop"

+++

So I have an infinite `for loop` however I want to use `defer` to make my intention clearer.

Problem is this `defer` would never run because my function wouldn't return. This, in turn, would give you memory leak.

As neat as it could be, you can use a closure/anonymous function for this.

```go
    for x := 0; x < 2; x++ {
        func() {
            defer fmt.Println(x)
        }()
    }
```

## Real world use case:

So what I'm trying to do is to have an infinite polling using `context` on [AWS SQS](https://docs.aws.amazon.com/sdk-for-go/v1/developer-guide/sqs-example-enable-long-polling.html) to add a timeout if something is wrong with SQS(this can be anything from network errors to aws being down, it can happen).

```go
package main

import (
	"context"
	"fmt"
	"time"

	"github.com/aws/aws-sdk-go/aws"
	"github.com/aws/aws-sdk-go/aws/session"
	"github.com/aws/aws-sdk-go/service/sqs"
)

func main() {
	sess := session.Must(session.NewSessionWithOptions(session.Options{
		SharedConfigState: session.SharedConfigEnable,
	}))

	svc := sqs.New(sess)

	var timeout int64 = 5

	// URL to our queue
	qURL := "https://sqs.eu-west-1.amazonaws.com/237546493679/test-context-timeout"

	for {
		func() {
			ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
			defer cancel()

			result, err := svc.ReceiveMessageWithContext(ctx, &sqs.ReceiveMessageInput{
				MaxNumberOfMessages: aws.Int64(10),
				QueueUrl:            &qURL,
				WaitTimeSeconds:     aws.Int64(timeout),
			})

			if err != nil {
				fmt.Println("Error", err)
				return
			}

			fmt.Printf("Received %d messages.\n", len(result.Messages))
			if len(result.Messages) > 0 {
				fmt.Println(result.Messages)
			}

		}()
	}
}
```
