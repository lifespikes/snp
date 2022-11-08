# Standards and Practices
All development standards and practices of LS and adjacent teams.

## What is this?
An experimental approach on documenting what we will call "The LifeSpikes Way". Similar to other repositories owned by working groups, SNP will feature an arrangement of markdown files that specify things like:

- Public standards we hold compliance towards
- Uniform LS toolchain and their configurations
- Design patterns and examples
- Usages of OSS libraries specific to us

Most importantly, the repository will feature **RFCs**. This is a way of involving the rest of the team in the design process of all software built hereon.

## RFCs

RFCs (request for comment) are "suggestions" made by different collaborators that can be discussed and reviewed by team members. Like other WGs, RFCs will come in the form of pull requests. 

The contents of the RFC itself should be contained within the markdown files located in the PR, and not the PR description.

An example of an RFC could be:

```markdown
- Author: Richard Hendricks <richard@piedpiper.net>
- Original Date: 11/08/2022

## My RFC summary
Hello this is an RFC to add Macadamia Nut to the cookies in the lunch room.

### Motivation
We know that chocolate chip is great, but, after reviewing with multiple other peers, and trying some samples, I believe we should begin to prioritize Macadamia Nut cookie inventory.

### Design Detail
First, the supply of macadamia nut cookie will need to be increased by notifying a vendor. Programatically speaking, we would expect this usage:

[ code goes here ]

[ other stuff ]

### Cost and Alternatives
We expect the increase of macadamia nut cookie to take about 2 weeks. An alternative would be to have each macadamia nut taster get their supply on their own.

### Unanswered Questions
I don't have any yet. The good thing is, I can always come back and change this!
```

There isn't anything religiously enforced in terms of RFCs as of now. Most importantly shar ofe your idea, explain why everyone else should like it, and then be open to critique and suggestions from others.

## RFC Approval
In the spirit of following the principles of the amazing Fred Brooks, do understand that SNP approval/rejection is still an aristocratic process. 

Final approval of an RFC will be subject to review and potential suggestions from @CristianHG2. This will probably change in the future.

## Things to Consider
The idea is for this to hopefully take shape and act as a catalyst for implementations. Examples are:
- Updates to config files of linters or static analyzers
- Updates to development tools
- Development of new, adjacent packages
