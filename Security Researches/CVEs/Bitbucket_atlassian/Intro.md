###### What Is Bitbucket?

Atlassian, the same company that created Confluence (which had a 2022 vulnerability we covered in a [Cybrary course](https://app.cybrary.it/browse/course/cve-series-confluence-rce-cve-2022-26134)), also created a Git-based source code repository management solution called Bitbucket. This allows teams to collaborate in a central location using features like access control, workflow control, pull requests, Jira integrations, and a Full Rest API (learn more in the [Atlassian documentation](https://confluence.atlassian.com/confeval/development-tools-evaluator-resources/bitbucket/bitbucket-what-is-bitbucket)). The Atlassian Bitbucket command injection vulnerability (CVE-2022-36804) is present within this API.

###### What is the Atlassian Bitbucket Command Injection Vulnerability?

According to the [CVE details](https://nvd.nist.gov/vuln/detail/CVE-2022-36804):

"Multiple API endpoints in Atlassian Bitbucket Server and Data Center 7.0.0 before version 7.6.17, from version 7.7.0 before version 7.17.10, from version 7.18.0 before version 7.21.4, from version 8.0.0 before version 8.0.3, from version 8.1.0 before version 8.1.3, and from version 8.2.0 before version 8.2.2, and from version 8.3.0 before 8.3.1 allows remote attackers with read permissions to a public or private Bitbucket repository to execute arbitrary code by sending a malicious HTTP request. This vulnerability was reported via our Bug Bounty Program by TheGrandPew."

Looking at [@TheGrandPew’s Twitter account](https://mobile.twitter.com/TheGrandPew/status/1562596397819785216), we can see where he was awarded for finding this CVE.

![CVE Bitbucket Image 1.1.1 RCE Tweet.png](https://images.ctfassets.net/kvf8rpi09wgk/5uNT9w2dteaNDYIV6QXMgC/6c76277a45cfbbfceef7e6ccc8892f9c/GrandPew_tweet_1.png)

This illustrates the benefits of a public bug bounty program, where security researchers have a mechanism to report a vulnerability they may find. If you look further back on the [researcher’s Twitter account](https://mobile.twitter.com/TheGrandPew/status/1547764079632457728), you can see he, at first, didn’t know who to report the bug to:

![GrandPew RCE.png](https://images.ctfassets.net/kvf8rpi09wgk/26Y56MvibXCcHPvwns7iIr/5c66f21ccdaa78c80c3af00b881c2663/GrandPew_RCE.png)

The base score of the CVE is 8.8 (CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H), which assumes that an attacker must authenticate to the application to exploit the vulnerability. Looking further into the issue, any repository that can be read and is set to public can be exploited by an unauthenticated attacker, thereby upping the CVSS score to 9.8 and making it critical.

###### The Significance of CVE-2022-36804

Even before we can exploit the vulnerability, we must find a public repository in Bitbucket at the <vulnerable_server>/rest/api/latest/repos endpoint. This should list project keys and repo names, which we will need to perform command injection through the API:

![CVE Bitbucket Image 1.1.3.png](https://images.ctfassets.net/kvf8rpi09wgk/35gRwsISykVbyFeYB0Y5Nn/cc85d1710bf339f41a4d068d78e0db30/Bitbucket_Request_Response.png)

Accessing the `/rest/api/latest/repos` endpoint and displaying the public project “CFX” and repo “hello-world”

The crux of the vulnerability lies in the <vulnerable_server>/rest/api/latest/projects/<projectKey>/repos/<repositorySlug>/archive endpoint, which is used to archive repositories. The API action uses the git-archive command (learn more [here](https://git-scm.com/docs/git-archive)). Injecting NULL bytes within the GET request allows for passing arbitrary commands on the server. This can be illustrated in the image below, where injecting the help command with NULL bytes (\x00 /%00) results in observing the help options for the git-archive command:

![CVE Bitbucket NULL.png](https://images.ctfassets.net/kvf8rpi09wgk/jhtUhiM2xx3HKxVY2Fr0k/2bb82ada4e6c5eaf9305c70ac58a60ed/Using_NULL_bites_in_the_prefix.png)

Using NULL bytes in the “prefix” parameters with the “help” command shows the output of the git-archive command

Leveraging this further, adding to the git archive command with the --remote=/ and --exec flags, along with a command enclosed in a backtick (60 in Hexadecimal), results in a server response of 500 with an error message showing part of the executed command:

![CVE Bitbucket Image 1.1.5.png](https://images.ctfassets.net/kvf8rpi09wgk/6fVTbBquhTbWXy9d8JSU4g/0c8f23ffecf35ccc3b384438d6070f93/Passing_the_ID_command.png)

Passing the “id” command in the GET request and observing part of the command echoed back in the response

Taking the command execution aspect out of the web context, we can see that git-archive can be used to execute arbitrary commands within a terminal:

![CVE Bitbucket Image 1.1.6.png](https://images.ctfassets.net/kvf8rpi09wgk/4Ufgs7S3oWNDYDYDcAvZ6/e93079e08f6e37ef7a1b7ba458236c66/Command_Execution.png)

As we learned in the [Confluence CVE course](https://app.cybrary.it/browse/course/cve-series-confluence-rce-cve-2022-26134), we can also use $(command) for command execution in a Unix environment. This is also the case with this Atlassian Bitbucket vulnerability, as long as we use the Hex equivalent, %24%28<command>%29:

![uname.png](https://images.ctfassets.net/kvf8rpi09wgk/3eRMujQXZyXtCoMXjEqhO7/e1a77d06aca6e6f985b759a0d5641bbc/uname.png)

Passing the “uname” command in the GET request and substituting backticks for $()

Rapid7 added a [Metasploit module](https://github.com/rapid7/metasploit-framework/pull/17042) for this CVE, but our lab will focus on how we can use out-of-band techniques to retrieve information on a server when we have limited visibility from our commands. As we saw above, only partial command output is visible in the error. But as (ethical!) hackers, we will think outside the box!