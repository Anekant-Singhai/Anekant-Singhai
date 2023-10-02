According to the [Atlassian website](https://confluence.atlassian.com/bitbucketserver/bitbucket-server-and-data-center-advisory-2022-08-24-1155489835.html), a temporary mitigation method is to turn off public repositories globally by enabling the setting, feature.public.access=false. This changes the attack vector from unauthorized to authorized. This should not be considered a complete fix, as users with valid accounts will still be able to exploit the vulnerability.

Atlassian recommends upgrading to fixed versions:

- [Bitbucket Server and Data Center 7.6](https://confluence.atlassian.com/bitbucketserver/bitbucket-server-7-6-release-notes-1018780800.html): Bug fix release 7.6.17 ( [LTS](https://confluence.atlassian.com/enterprise/long-term-support-releases-948227420.html)) or newer
- [Bitbucket Server and Data Center 7.17](https://confluence.atlassian.com/bitbucketserver/bitbucket-data-center-and-server-7-17-release-notes-1086401305.html): Bug fix release 7.17.10 ( [LTS](https://confluence.atlassian.com/enterprise/long-term-support-releases-948227420.html)) or newer
- [Bitbucket Server and Data Center 7.21](https://confluence.atlassian.com/bitbucketserver/bitbucket-data-center-and-server-7-21-release-notes-1115129015.html): Bug fix release 7.21.4 ( [LTS](https://confluence.atlassian.com/enterprise/long-term-support-releases-948227420.html)) or newer
- [Bitbucket Server and Data Center 8.0](https://confluence.atlassian.com/bitbucketserver/bitbucket-data-center-and-server-8-0-release-notes-1115659343.html): Bug fix release 8.0.3 or newer
- [Bitbucket Server and Data Center 8.1](https://confluence.atlassian.com/bitbucketserver/bitbucket-data-center-and-server-8-1-release-notes-1130726463.html): Bug fix release 8.1.3 or newer
- [Bitbucket Server and Data Center 8.2](https://confluence.atlassian.com/bitbucketserver/bitbucket-data-center-and-server-8-2-release-notes-1130729887.html): Bug fix release 8.2.2 or newer
- [Bitbucket Server and Data Center 8.3](https://confluence.atlassian.com/bitbucketserver/bitbucket-data-center-and-server-8-3-release-notes-1141987753.html): Bug fix release 8.3.1 or newer

[Rapid7’s technical analysis](https://attackerkb.com/topics/iJIxJ6JUow/cve-2022-36804/rapid7-analysis) shows that the fix filters out NULL bytes (\x00/%00) in command arguments. As you observed in the previous lesson, the NULL bytes are necessary to perform RCE.

Further guidance on upgrading Bitbucket can be found [here](https://confluence.atlassian.com/bitbucketserver/bitbucket-server-upgrade-guide-776640551.html). Due to the severity of this exploit, it is recommended organizations implement the patch as soon as possible.

###### Summary

This course covered how to exploit CVE-2022-36804 by using publicly available exploit code, as well as how to use an out-of-band server to retrieve the output of commands executed on the vulnerable server. We also covered how defenders can mitigate this vulnerability. You have now seen how easy it is to compromise Bitbucket using exploit code, and you can communicate the impact of this CVE to stakeholders.
