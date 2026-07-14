# Entra ID & Intune MDM Lab

## Objective
Provisioned a Microsoft Entra ID tenant and Intune MDM environment to simulate
managing corporate identities and device compliance in a modern cloud
environment — the workflow an IT support / sysadmin role would use to
onboard, secure, and enforce compliance on end-user devices.

## Environment
- Microsoft Entra ID (Entra ID P2 trial)
- Microsoft Intune (MDM)
- 1x enrolled iPhone 11

## What I did

### 1. Provisioned the tenant and created test identities
Signed up for a Microsoft Intune trial, which provisioned a new Entra ID
tenant (`LabCA`) with Entra ID P2 and Intune included.

![Entra tenant overview](screenshots/01-entra-tenant-overview.png)

Created two test users to simulate a small user base.

![Create user - Jane Doe](screenshots/02-create-user-jdoe.png)
![Create user - John Smith](screenshots/03-create-user-jsmith.png)
![Users list](screenshots/04-users-list.png)

### 2. Created a security group for targeted policy assignment
Rather than assigning policies to "All users," created a scoped security
group (`MDM-Test-Devices`) to control exactly which identities and devices
the lab policies apply to — standard practice for phased rollouts.

![New group](screenshots/05-new-group-mdm-test-devices.png)
![Group overview](screenshots/06-group-overview.png)

### 3. Configured a Conditional Access policy requiring MFA
Built a Conditional Access policy requiring multifactor authentication for
all users, deployed in Report-only mode to validate impact before enforcing.

![Conditional Access - MFA grant control](screenshots/07-ca-policy-mfa-grant.png)
![Conditional Access policy list](screenshots/08-ca-policies-list.png)

### 4. Confirmed Intune as the MDM authority
Verified the tenant's MDM authority was set to Microsoft Intune before
attempting device enrollment.

![Intune tenant status - MDM authority](screenshots/09-intune-mdm-authority.png)

### 5. Configured the Apple MDM Push certificate
iOS/iPadOS enrollment requires an Apple MDM Push certificate as a
prerequisite. Generated a CSR in Intune, created the certificate via the
Apple Push Certificates Portal, and uploaded it back to activate iOS device
management for the tenant.

![Apple enrollment prerequisites](screenshots/10-apple-enrollment-prerequisites.png)
![Apple MDM Push Certificate setup](screenshots/11-apple-mdm-push-cert-setup.png)

### 6. Enrolled a test device
Enrolled a personal iPhone into Intune via the Company Portal app —
downloaded and installed the management profile, then confirmed successful
registration.

![Downloading management profile](screenshots/12-enrollment-download-profile.png)
![Installing management profile](screenshots/13-enrollment-install-profile.png)
![Enrollment success](screenshots/14-enrollment-success-registered.png)
![Device enrolled in Intune](screenshots/15-device-enrolled.png)

### 7. Built and assigned a device compliance policy
Created an iOS compliance policy blocking jailbroken devices and enforcing
a minimum OS version (26.5.2), assigned to the `MDM-Test-Devices` group.

![Compliance policy settings](screenshots/16-compliance-policy-settings.png)
![Compliance policy assignments](screenshots/17-compliance-policy-assignments.png)
![Compliance policy review and create](screenshots/18-compliance-policy-review-create.png)

### 8. Built and assigned a configuration profile
Created a device restrictions profile enforcing a PIN/passcode requirement
(minimum length 6, no simple passwords), assigned to the same group.

![Configuration profile - PIN requirement](screenshots/19-configuration-profile-pin.png)

### 9. Verified device compliance status
The device initially failed the compliance check because it was running an
OS version below the policy's minimum requirement — confirming the policy
was actively enforcing, not just configured. After updating, the device
re-evaluated as compliant.

![Compliance check flagging outdated OS](screenshots/20-compliance-check-os-update-required.png)
![Device compliance status - Compliant](screenshots/21-device-compliance-status.png)

### 10. Tied Conditional Access to device compliance (Zero Trust)
Extended the Conditional Access setup with a policy requiring a device to
be marked compliant before granting access to a target application — linking
identity (Entra ID) and device posture (Intune) into a single access
decision.

![Conditional Access - require compliant device](screenshots/22-ca-policy-compliant-device-grant.png)
![Conditional Access policy details](screenshots/23-ca-policy-compliant-device-details.png)

## Key concepts demonstrated
- Cloud identity administration (Entra ID users, groups, Conditional Access)
- Mobile Device Management (MDM) enrollment, including Apple MDM Push
  certificate configuration
- Device compliance policies vs. configuration profiles
- Zero Trust: gating application access on device compliance, not just user
  identity
- Verifying policy enforcement end-to-end — the compliance policy correctly
  caught a real out-of-date device before it passed

## Notes
Built in a Microsoft-provided free trial tenant (Entra ID P2 + Intune) for
skills demonstration purposes. Conditional Access policies were run in
Report-only mode to validate behavior without risking lockout in a live
environment — standard practice before enforcing a new policy.
