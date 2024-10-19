# Day-25-Integrating-OS-Ticket-with-Elastic-Stack

In this guide, I’ll walk you through how I successfully integrated OS Ticket with my Elastic Stack. This integration automates ticket creation in OS Ticket whenever an alert is triggered in Elastic, enhancing your incident management and tracking workflow. Let’s dive in!

## Step 1: Access Your OS Ticket Admin Panel
1. **Login to your OS Ticket instance.**
   
2. **Navigate to the Admin Panel:**
   - Click on **Admin** at the top right corner of the page.

3. **Add a New API Key:**
   - Under the Admin Panel, click on **Manage** and select **API**.
   - Click **Add New API Key**.
   - For the IP address, use your ELK server’s private IP, as both the OS Ticket server and ELK server are in the same Virtual Private Cloud (VPC). If they were not in the same VPC, you would use the public IP instead.

   **Note:** If you forgot your ELK server’s private IP, you can retrieve it from Vultr:
   - Go to Vultr and select your ELK server.
   - On the left side, click on **Settings** and then select **VPC 2.0**. The private IP will be listed here.
   - Copy the private IP and paste it into the OS Ticket API form.

4. **Set Permissions and Notes:**
   - Enable **Create Tickets** under the Services section.
   - For Internal Notes, you can add any description you like. In this example, we’ll use “Phoenix 30-Day Elastic Connector.”
   - Click **Add Key**.
   - Now that the API key is created, copy it and save it in a notepad for future use.

## Step 2: Configure Elastic for API Integration
1. **Log into Elastic:**
   - Click on the hamburger icon (menu) and scroll down to the **Management** tab.

2. **Navigate to Stack Management:**
   - Under **Alerts and Insights**, select **Connectors**.
   - Click on **Create Connector**.

   **Important:** Elastic’s free tier does not support API integration. You need to activate a 30-Day Free Trial to enable this feature.

3. **Activate the 30-Day Trial:**
   - Go to **Manage License** and select **Start a 30-Day Trial**.
   - Once the trial is active, you’ll have access to third-party integrations, including API connectors.

4. **Create a Webhook Connector:**
   - Click on **Create Connector**.
   - Select **Webhook** as your connector type.
   - Name the connector “OS Ticket.”
   - For the Post Request URL, use your OS Ticket’s private IP. The format should be:

    ```bash
    http://(Private-IP-OS-Ticket)/osTicket/upload/api/tickets.xml
    ```
   
5. **Configure Authentication:**
- Select **None** under Authentication.
- Add an HTTP header:
  - **Key:** `X-API-Key`
  - **Value:** Your OS Ticket API Key (which you copied earlier).
- Click **Save and Test**.

Elastic will then prompt you to create an action and define the body of the request. You can find an example body on the OS Ticket GitHub page to ensure the format is correct.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ticket alert="true" autorespond="true" source="API">
    <name>Angry User</name>
    <email>api@osticket.com</email>
    <subject>MYDFIR-OSTicket</subject>
    <phone>318-555-8634X123</phone>
    <message type="text/plain"><![CDATA[Message content here]]></message>
    <attachments>
        <file name="file.txt" type="text/plain"><![CDATA[
            File content is here and is automatically trimmed
        ]]></file>
        <file name="image.gif" type="image/gif" encoding="base64">
            R0lGODdhMAAwAPAAAAAAAP///ywAAAAAMAAwAAAC8IyPqcvt3wCcDkiLc7C0qwy
            GHhSWpjQu5yqmCYsapyuvUUlvONmOZtfzgFzByTB10QgxOR0TqBQejhRNzOfkVJ
            +5YiUqrXF5Y5lKh/DeuNcP5yLWGsEbtLiOSpa/TPg7JpJHxyendzWTBfX0cxOnK
            PjgBzi4diinWGdkF8kjdfnycQZXZeYGejmJlZeGl9i2icVqaNVailT6F5iJ90m6
            mvuTS4OK05M0vDk0Q4XUtwvKOzrcd3iq9uisF81M1OIcR7lEewwcLp7tuNNkM3u
            Nna3F2JQFo97Vriy/Xl4/f1cf5VWzXyym7PHhhx4dbgYKAAA7
        </file>
    </attachments>
    <ip>123.211.233.122</ip>
</ticket>
## Step 3: Update OS Ticket Network Settings

Before testing the integration, update the OS Ticket server’s IP address configuration:

1. **Connect to your OS Ticket server via RDP.**

2. **Change IP Settings:**
   - Open **Control Panel** > **View Network Status and Tasks**.
   - Click on **Change Adapter Settings**.
   - Select **Ethernet Instance 0** > **Properties**.
   - Choose **Internet Protocol Version 4 (TCP/IPv4)** and select the option to manually enter an IP address.
   - Input the private IP address for your OS Ticket server (you can find it in Vultr under **Settings > VPC 2.0**).
   - Click **OK** and **Apply**.

## Step 4: Test the Integration

1. **Run a Test in Elastic:**
   - Go back to Elastic, and click **Run Test**.
   - If successful, the test will generate a ticket in OS Ticket.

2. **Verify in OS Ticket:**
   - Log into your OS Ticket Admin Panel and check the ticketing section.
   - You should see a new ticket titled **Pheonix-30DayChallenge-Pheonixrocks** (or the subject name you provided).

---

Congratulations! You’ve successfully integrated OS Ticket with Elastic. This allows you to automatically generate tickets from Elastic alerts, improving your auditing and incident tracking.

## Recap: What We’ve Accomplished
- Spun up Elastic and Kibana instances.
- Set up two servers (Windows and Linux) and installed agents to push data to Elastic.
- Created alerts and dashboards for monitoring brute force attacks on SSH and RDP.
- Identified common Command and Control (C2) frameworks, like Mythic.
- Integrated a ticketing system (OS Ticket) into the stack to manage and track alerts.

By following these steps, you’ve created a fully functioning mini-Security Operations Center (SOC) environment with Elastic and OS Ticket.

In my next blog, we will dive into investigating the alerts we’ve set up, starting with the SSH brute force alerts. Stay tuned!

I hope this guide helped you integrate OS Ticket into your tech stack. If you have any questions or need further clarification, feel free to leave a comment!

