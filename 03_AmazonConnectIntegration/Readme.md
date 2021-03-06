# Module 3: Integrate Amazon Lex with Amazon Connect
In this module you will integrate your Lex bot with Amazon Connect, a contact center service that makes it easy for any business to deliver better customer service at lower cost.

At completion of this module you will be able to interact with your Lex bot through the plain old telephone service.
To achieve this you will create a contact center, configure a simple call flow, and assign a public phone number to the flow. 

## Implementation Instructions

Each of the following sections provide an implementation overview and detailed, step-by-step instructions. The overview should provide enough context for you to complete the implementation if you're already familiar with the AWS Management Console or you want to explore the services yourself without following a walkthrough.

### Create a new Amazon Conect contact centre instance
An Amazon Connect instance is the starting point for your contact center.

Go to the [Amazon Connect Console](https://console.aws.amazon.com/connect/home?region=us-east-1) to create a new virtual contact center instance in the us-east-1 (Virgina) region.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. From the AWS Management Console, choose **Services** then select **Amazon Connect** under Contact Center.
2. Choose **Add an instance**.
3. In **Step 1: Identity management**, select **Store users within Amazon Connect** and type a domain name (e.g. `{YourFirstName}ContactCentre` to complete **Access URL**, Select **Next step**.
	
	> The domain name used in your contact center URL needs to be globally unique and cannot be changed.
	Alternatively, Amazon Connect can leverage an existing [AWS Directory Services](https://aws.amazon.com/directoryservice) directory.   
	
	
5. In **Step 2: Create an Administrator**, select **Skip this**, and select **Next step**. 
6. In **Step 3: Telephony Options**, select **I want to handle incoming calls with Amazon Connect** and **I want to make outbound calls with Amazon Connect**.
7. In **Step 4: Data storage**, keep the defaults and select **Next step**.
8. In **Step 5: Review and Create**, review your settings and then select **Create Instance**. 
</p></details>

### Claim a phone number for your Amazon Connect instance
Once your Amazon Connect instance has been created, select **Get started** to select a phone number. 
<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. Select **Get started** to open the Amazon Connect Contact Center Manager (CCM) Welcome screen.
2. Select **Let's go** to claim a phone number.
3. Select **United States +1**, **Direct Dial**, and choose a phone number from the numbers provided. Select **Next**
4. Dial the phone number you selected in step 3 from another phone (e.g. your mobile phone) and choose **1** from the voice menu to connect with an agent. You can then use the Amazon Connect Contact Control Panel to accept the call.
5. Select **Continue**. This will open the Amazon Connect console.
</p></details>

### Configure contact flow
With your contact centre instance set-up and a phone number claimed, you can now create the call flow that integrates with the Amazon Lex bot we created in [Module  1](../01_LexBotInformational).

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. In the Amazon Connect Contact Center Manager, use the navigation pane on the left hand side to select **Routing** and **Contact flows**.

	![ContactFlowNavigation](images/contact_flows_navigation.png)
	
	> If you closed your browser window you can always re-open the Amazon Connect Contact Center Manager from the [Amazon Connect console](https://console.aws.amazon.com/connect/home?region=us-east-1). Just selct your Amazon connect instance and click on the **Login as administrator** button in the **Overview** section of the console. 

1. In the top right corner select **Create contact flow** to open the contact flow editor.
2. Name your contact flow `CustomerServiceChatbot` and give it a description.

	![ContactFlowNaming](images/contact_flow_naming.png)

3. Expand the **Interact** group of blocks and drag and drop the **Get customer input** block onto the grid.
4. Expand the **Terminate / Transfer** group of blocks and drag and drop the **Disconnect / Hang up** block onto the grid.
5. Wire up the three building blocks as shown in the image below.

	![ContactFlowWiring](images/contact_flow_wiring.png)

1. Double click on the **Get customer input** block to access its configuration. 
	1. Select the **Text to speech (Ad hoc)** input type and enter the below as a welcome message for your callers:
		` Welcome to the marvelous telco company. How can I help you today?`
	2. Select **Amazon Lex** input type
	3. Enter `InternationalPlan` bot name and `dev` Alias
	5. Click **Save**
	
		![ContactFlowInput](images/get_customer_input_prompt.png)
		
		![ContactFlowInput](images/get_customer_input_input.png)
	
1. Click on the **down arrow** (![DownArrow](images/down.png)) next to the save button and select **Save & Publish**
1. Confirm publishing of the workflow in selcting the **Save & publish** button.
</p>

</details>


### Associate contact flow with phone number
Now you need to associate your new contact flow with your phone number
<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. Select **Routing** and **Phone Numbers** on the left hand Amazon Connect navigation pane.
2. Click on the number to edit the contact flow
3. Search and select the `CustomerServiceChatbot` contact flow in the **Contact flow/IVR** field.
4. Select **Save** to confirm the contact flow association.
</p></details>

### Enable Amazon Lex integration for your contact flow 
As a final step we need to enable the Amazon Lex integration point for our Amazon Connect instance in the Amazon Connect console.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. Open the [Amazon Connect console](https://console.aws.amazon.com/connect/home?region=us-east-1) and select your Amazon Connect instance.
2. Select **Contact flows** on the left hand navigation.
3. In the **Amazon Lex** section select **+ Add Lex Bot**, select the `InternationalPlan` bot and click on **Save Lex Bots**.
</details>


### Test your Amazon Lex enabled Amazon Connect contact flow
Dial your Amazon Connect contact center phone number to confirm functionality of contact flow and Amazon Lex integration. Tell the virtual service agent **"I am going to China."**, use 1234 when asked for your pin code.

### Extend contact flow integration to submit dialer ID to Amazon Lex 
In this last step we are enhancing the customer input configuration of the contact flow to submit the dialer ID to Amazon Lex for authentication.

 ![ContactFlowNavigation](images/set_session_attributes.png)

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. Re-open the Amazon Connect Dashboard. Within the [Amazon Connect console](https://console.aws.amazon.com/connect/home?region=us-east-1) select **Overview** and **Login as administrator**
2. On the left hand navigation select **Routing** **Contact flows**.

	![ContactFlowNavigation](images/contact_flows_navigation.png)
	
3. Click on the 'CustomerServiceChatbot' flow to open the flow.

4. Double click on the **Get customer input** block to access its configuration.
5. Scroll to the bottom and under **Session attributes** click **Add a parameter**
6. Select **Send attribute**
7. In the **Type** drop-down, select **System**, Enter  `IncomingNumber` in the **Key** field and select **Customer Number** from the **Attribute** drop down.
8. Click **Add another Parameter**
9. Enter `Source` as **Key** and `AmazonConnect` as **Value**
10. Select **Save**
11. Click on the **down arrow** (![DownArrow](images/down.png)) next to the save button and select **Save & Publish**
	
12. Confirm publishing of the workflow in selecting the **Save & publish** button.
 	![ContactFlowNavigation](images/publish_confirmation.png)
</details>	
	
### Test your Amazon Lex enabled Amazon Connect contact flow
Now re-test the contact flow and Amazon Lex integration with authentication enabled. Dial your Amazon Connect contact center phone number again to confirm functionality. Ask the virtual service agent **"What iternational plans do you have?"**. When asked for your pin code enter the last four digits of the mobile number you are calling from.	
	




