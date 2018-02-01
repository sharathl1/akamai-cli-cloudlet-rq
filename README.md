# Akamai CLI for Request Control Cloudlet
Provides a way to interact real-time with your Request Control Cloudlet via Open APIs and without manually having to go into the Luna Portal. Provides various functionality such as viewing current policies, current status, rule details, and the ability to make changes.

*NOTE:* This tool is intended to be installed via the Akamai CLI package manager, which can be retrieved from the releases page of the [Akamai CLI](https://github.com/akamai/cli) tool.

### Local Install, if you choose not to use the akamai package manager
* Python 3+
* pip install edgegrid-python

### Credentials
In order to use this configuration, you need to:
* Set up your credential files as described in the [authorization](https://developer.akamai.com/introduction/Prov_Creds.html) and [credentials](https://developer.akamai.com/introduction/Conf_Client.html) sections of the Get Started pagegetting started guide on developer.akamai.com, the developer portal.  
* When working through this process you need to give grants for the property manager API and the User Admin API (if you will want to move properties).  The section in your configuration file should be called 'papi'.

### Overview

```
blr-mp0ui:~ sharao$ akamai akamai-request-control
Usage: akamai akamai-request-control <command> <args> [options]

Commands:
  setup                  		Download of Request Control Cloudlet policyIds and groupIds
  list                			List current Request Control Cloudlet policy names
  show       					Get specific details for a policy name
  activate     					Activate a specified version for a policy
  download 						Download the raw policy rules for a specified policy version
  create-version 				Activate a specified version for a policy
  addRule 						Adds a new rule to a specified version for a policy
  modifyRule 					Modifies a rule to a specified version for a policy
  deleteRule 					Deletes a rule to a specified version for a policy

Command options:
  --edgerc <config>    Config file                		   [file] [default: $HOME/.edgerc]
  --section <section>  Config section                             [string] [default: papi]
  --debug <debug>      Turn on debugging.                                        [boolean]
  --help               Show help                                [commands: help] [boolean]
  --version            Show version number                   [commands: version] [boolean]

Copyright (C) Akamai Technologies, Inc
Visit http://github.com/akamai/cli-cloudlet-request-control for detailed documentation
```

## cli-cloudlet-request-control
Main program that wraps this functionality in a command line utility:
* [setup](#setup)
* [list](#list)
* [show](#show)
* [activate](#activate)
* [download](#download)
* [create-version](#create-version)
* [addRule](#addRule)
* [modifyRule](#modifyRule)
* [deleteRule](#deleteRule)



### setup
Does a one time download of Request Control Cloudlet policyIds and groupIds and stores them in /setup folder for faster local retrieval. This command can be run anytime and will refresh the /setup folder based on the current list of policies. 

```bash
%  akamai-request-control setup
```

### list
List all current Request Control Cloudlet policy names for all groups for the specific account

```bash
%  akamai-request-control list
```
The flags of interest for show are:

```
--groupid <groupid>        Lists policy name for a specific groupid
```


### show
Get specific details for a policy name. Available information include configurations that reference that policy, current version numbers on Akamai staging and production, version history, and current rule settings.

```bash
%  akamai-request-control show --policy samplePolicyName
%  akamai-request-control show --policy samplePolicyName --from-version 37
%  akamai-request-control show --policy samplePolicyName --version 66
%  akamai-request-control show --policy samplePolicyName --version 66 --verbose
```

The flags of interest for show are:

```
--policy <policyName>        Specified Request Control Cloudlet policy name
--version <version>          Specific version number for that policy name (optional)
--from-version <fromVersion> If --version is not specified, list policy version details starting from --from-version value (optional)
--verbose                    If --version is specified, add --verbose to get full rule details including url paths and match criteria (optional)

```

### activate
Activate a specified version for a policy to the appropriate network (staging or production)

```bash
%  akamai-request-control activate --policy samplePolicyName --version 87 --network staging
%  akamai-request-control activate --policy samplePolicyName --version 71 --network production
```

The flags of interest for activate are:

```
--policy <policyName>   Specified Request Control Cloudlet policy name
--version <version>     Specific version number for that policy name
--network <network>     Either staging or production

```

### download
Download the raw policy rules for a specified version in json format for local editing if desired.

```bash
%  akamai-request-control download --policy samplePolicyName --version 87
%  akamai-request-control download --policy samplePolicyName --version 71 --output-file savefilename.json
```

The flags of interest for download are:

```
--policy <policyName>     Specified Request Control Cloudlet policy name
--version <version>       Specific version number for that policy name
--output-file <filename>  Filename to be saved in /rules folder (optional) 

```

### create-version
Create a new policy version from a raw json file

```bash
%  akamai-request-control create-version --policy samplePolicyName
%  akamai-request-control create-version --policy samplePolicyName --file filename.json
%  akamai-request-control create-version --policy samplePolicyName --file filename.json --force
```

The flags of interest for create-version are:

```
--policy <policyName>  Specified Request Control Cloudlet policy name
--file <file>	       Filename of raw .json file to be used as policy details. This file should be in the /rules folder (optional)
--force                Use this flag if you want to proceed without confirmation if description field in json has not been updated
```


### addRule
Add a new rule to a specific version in the specified policy.

```bash
%  akamai-request-control addRule --policyName samplePolicyName --rule 'ruleName' --file rules.json
%  akamai-request-control addRule --policyName samplePolicyName --rule 'ruleName' --allowIP '1.2.3.4,5.6.7.8/30'
%  akamai-request-control addRule --policyName samplePolicyName --rule 'ruleName' --denyCountry 'IN,DE'
%  akamai-request-control addRule --disable --policyName samplePolicyName --rule 'ruleName' --giveBrandedResponseForCountry 'PK'

```

The flags of interest for addRule are:

```
--disable               		Adds the rule and disables the rule in the policy
--policy <policyName>   		Specified Request Control Cloudlet policy name
--rule <ruleName>       		Name of rule in policy that should be added. Use single quotes ('') in case rule name has spaces.
--file <file>	        		Filename of raw .json file to be used as rules details. This file should be in the /rules folder (optional)
--allowIP						List of IPs or CIDR blocks to be allowed separated by commas(,) within single quotes('')
--denyIP						List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes('')
--giveBrandedResponseForIP		List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes('')
--allowCountry					List of country codes(case-insensitive) to be allowed separated by commas(,) within single quotes('')
--denyCountry					List of country codes(case-insensitive) to be blocked separated by commas(,) within single quotes('')
--giveBrandedResponseForCountry	List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes('')
```

### modifyRule
Add a new rule to a specific version in the specified policy.

```bash
%  akamai-request-control modifyRule --policyName samplePolicyName --rule 'ruleName' --file rules.json
%  akamai-request-control modifyRule --policyName samplePolicyName --rule 'ruleName' --allowIP '1.2.3.4,5.6.7.8/30'
%  akamai-request-control modifyRule --policyName samplePolicyName --rule 'ruleName' --denyCountry 'IN,DE'
%  akamai-request-control modifyRule --disable --policyName samplePolicyName --rule 'ruleName' --giveBrandedResponseForCountry 'PK'

```

The flags of interest for modifyRule are:

```
--disable               		Disables the rule in the policy
--policy <policyName>   		Specified Request Control Cloudlet policy name
--rule <ruleName>       		Name of rule in policy that should be modified. Use single quotes ('') in case rule name has spaces.
--file <file>	        		Filename of raw .json file to be used as rules details. This file should be in the /rules folder (optional)
--allowIP						List of IPs or CIDR blocks to be allowed separated by commas(,) within single quotes(''). This will replace the current allowed IP/CIDR list if any.
--denyIP						List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes(''). This will replace the current denied IP/CIDR list if any.
--giveBrandedResponseForIP		List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes(''). This will replace the current IP/CIDR list if any.
--allowCountry					List of country codes(case-insensitive) to be allowed separated by commas(,) within single quotes(''). This will replace the current allowed country list if any.
--denyCountry					List of country codes(case-insensitive) to be blocked separated by commas(,) within single quotes(''). This will replace the current denied country list if any.
--giveBrandedResponseForCountry	List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes(''). This will replace the current country list if any.
```

### deleteRule
Add a new rule to a specific version in the specified policy.

```bash
%  akamai-request-control deleteRule --policyName samplePolicyName --rule 'ruleName'
```

The flags of interest for deleteRule are:

```
--policy <policyName>   		Specified Request Control Cloudlet policy name
--rule <ruleName>       		Name of rule in policy that should be modified. Use single quotes ('') in case rule name has spaces.
```

## Caveats/Info

* Before we can execute these commands, please ensure we have at least one Request Control Cloudlet policy and version already created in the Luna Portal.
* You can find more about Request Control Cloudlet in here - https://control.akamai.com/dl/customers/Cloudlets/RequestControlQuickRef.pdf
https://control.akamai.com/dl/customers/Cloudlets/RequestControlGuide.pdf
* You can find more about Cloudlets APIs in here - https://developer.akamai.com/api/luna/cloudlets/overview.html
