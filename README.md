# Add-Webhooks-to-Shopify-Remix-App
How To Add Webhooks To Your Shopify Remix App

## Step1: Create a remix shopify app using Shopify CLI
## Step 2: Add / Subscribe your webhook to shopify.app.toml file 

For example:

            [webhooks]
            api_version = "2025-01"
        
            [[webhooks.subscriptions]]
            topics = [ "orders/create" ]
            uri = "/webhooks/app/orders-create"
        
            [[webhooks.subscriptions]]
            topics = [ "app/scopes_update" ]
            uri = "/webhooks/app/scopes_update"
        
            [[webhooks.subscriptions]]
            topics = [ "app/uninstalled" ]
            uri = "/webhooks/app/uninstalled"

## Step 3: Create webhooks.app.orders-create.jsx file under route folder and add this code
      
              import { authenticate } from "../shopify.server";
              import db from "../db.server";
          
              export const action = async ({ request }) => {
              const { shop, payload, session, topic } = await authenticate.webhook(request);
          
              console.log(`Received ${topic} webhook for ${shop}`);
              console.log("Webhook Payload:", JSON.stringify(payload, null, 2));
          
            // Extract order details
              const orderData = {
                shop,
                orderId: String(payload.id),
                customerName: payload.customer?.first_name + " " + payload.customer?.last_name || "Guest",
                email: payload.customer?.email || null,
                totalPrice: payload.total_price,
                currency: payload.currency,
              };
          
            // Log the order data
            console.log("Order Data:", JSON.stringify(orderData, null, 2));
          
            // Webhook requests can trigger multiple times and after an app has already been uninstalled.
            // If this webhook already ran, the session may have been deleted previously.
            if (session) {
              await db.session.deleteMany({ where: { shop } });
            }
          
            return new Response();
          };


## 4. Deploy the app using shopify app deploy command
## 5. Reset the app by this command Shop npm run shopify app dev -- --reset     
## 6. Test it by placing a test order via your storefront. 
## 7. Check the log console of your code editor. 
