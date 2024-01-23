# learn_aws_associate_developer
repo for adding interesting codes and documenting the information for the certification

# starting date 22 January 2024
according to the website, for 24h per week of learning, the candidate is considered ready for presenting the exam the **5 of february**

# IAM

## Users
There are:
 - Users: which we can add specific policies
 - Groups: add users to one or multiple groups, each group having a set of permissions
 - Roles: Give a role to a service or person to have a set of permissions depending on their execution

 ## Login
 there exists:
 - MultiFactorAuthentication
 - single URL login for the different users, all they need to do is go to the link and put their login and password
 - It is also possible to set rules to the passwords like complexity, length and expire time

 # EC2

 ## Pricing
 the pricing of the service EC2 depends on the type of demand:
 - On demand: pay as much as you use
 - Reserved: you make a commitment of certains machines of certain type for a N amount of time (save money++), possibility to upgrade to higher machine
 - Spot: you use machines that are available at the moment and there is not so much demand so the price is less but if the demand is higher, you machine is terminated or freezes
 - Dedicated: the machine is yours but is more expensive, only use if strict restrictions or softwares with one machine license 

 