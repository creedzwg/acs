# Upgrade sub-account policy {#concept_uyq_gs2_xdb .concept}

Container Service comprehensively upgrades the security authorization management on January 15 2018, and provides cross-service authorization based on STS to provide you with more secure services. If you have used Container Service before 15 January 2018, the system completes the authorization by default. For more information about the granted permissions, see [Role authorization](reseller.en-US/User Guide/Authorizations/Role authorization.md#) If you used Container Service with a sub-account before, grant the sub-account the permissions to use Container Service again.

Container Service can automatically upgrade the sub-account policy. With this feature, Container Service automatically grants your sub-accounts the AliyunCSReadOnlyAccess permission. You can also select to manually grant permissions to your sub-accounts in the Resource Access Management \(RAM\) console.

## Upgrade sub-account policy {#section_wfn_ks2_xdb .section}

1.  Use the primary account to log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Use the primary account to log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  Click **Upgrade** sub account policy in the upper-right corner on the Overview page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6985/15561567984742_en-US.png)

4.  Click **OK** in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6985/15561567984744_en-US.png)

    Container Service will grant your sub-accounts the corresponding roles when the sub-account policy is being upgraded.

    If the upgrade fails, a dialog box listing the sub-accounts that fail to be upgraded appears.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6985/15561567994746_en-US.png)

    Click **Upgrade sub account policy** to try to upgrade again or go to the RAM console to manually grant permissions to sub-accounts.


## Grant permissions to sub-accounts in RAM console {#section_qnt_ls2_xdb .section}

1.  Use the primary account to log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Use the primary account to log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  Click **Users** in the left-side navigation pane.
4.  Click **Authorize** at the right of the sub-account.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6985/15561567994749_en-US.png)

5.  Select the authorization policy and click 1 to add the policy to the **Selected Authorization Policy Name**. Click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6985/15561567994750_en-US.png)

    Container Service provides two system authorization policies:

    -   AliyunCSFullAccess: Provides full access to Container Service.
    -   AliyunCSReadOnlyAccess: Provides read-only access to Container Service.
    You can also create custom authorization policies as per your needs and grant the policies to the sub-accounts. For more information, see [Create custom authorization policies](reseller.en-US/User Guide/Authorizations/Create custom authorization policies.md#).


