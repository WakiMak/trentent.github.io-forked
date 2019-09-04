---
id: 1244
title: List of exportable AD attributes
date: 2016-04-25T12:02:33-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/680-revision-v1/
permalink: /2016/04/25/680-revision-v1/
---
It appears the following AD attributes are exportable from LDIFDE or CSVDE:

DN,objectClass,ou,distinguishedName,name,description,sAMAccountName,objectCategory,instanceType,whenCreated,whenChanged,uSNCreated,uSNChanged,dSCorePropagationData,cn,member,groupType,displayName,info,extensionAttribute1,managedBy,publicDelegatesBL,extensionAttribute14,extensionAttribute15,mail,sn,givenName,homeMTA,proxyAddresses,homeMDB,garbageCollPeriod,mDBUseDefaults,mailNickname,protocolSettings,internetEncoding,userAccountControl,badPwdCount,codePage,countryCode,badPasswordTime,lastLogoff,lastLogon,pwdLastSet,primaryGroupID,accountExpires,logonCount,showInAddressBook,legacyExchangeDN,userPrincipalName,textEncodedORAddress,msExchHomeServerName,msExchMailboxSecurityDescriptor,msExchUserAccountControl,msExchMailboxGuid,msExchPoliciesIncluded,msExchMailboxAuditLogAgeLimit,msExchRecipientDisplayType,msExchAddressBookFlags,msExchRBACPolicyLink,msExchDumpsterQuota,msExchArchiveQuota,msExchRecipientTypeDetails,msExchMDBRulesQuota,msExchTransportRecipientSettingsFlags,msExchArchiveWarnQuota,msExchDumpsterWarningQuota,msExchUMEnabledFlags2,msExchModerationFlags,msExchProvisioningFlags,msExchUMDtmfMap,msExchBypassAudit,msExchMailboxAuditEnable,msExchWhenMailboxCreated,msExchTextMessagingState,reportToOriginator,msExchRequireAuthToSendTo,msExchALObjectVersion,msExchArbitrationMailbox,msExchCoManagedByLink,msExchHideFromAddressLists,msExchGroupDepartRestriction,msExchGroupJoinRestriction,reportToOwner,replicatedObjectVersion,replicationSignature,msExchADCGlobalNames,dLMemDefault,oOFReplyToOriginator,msExchPoliciesExcluded,delivContLength,authOrig,dLMemSubmitPerms,dLMemSubmitPermsBL,displayNamePrintable,altRecipientBL,adminCount,hideDLMembership,managedObjects

I&#8217;ve exported using CSVDE using all these attributes and managed to import back into a different AD domain (and finding and replacing DC=XXX,DC=COM) and these attributes appear to import cleanly without error

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->