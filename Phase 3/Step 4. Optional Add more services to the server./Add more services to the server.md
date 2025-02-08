# Step 4: Optional Add More Services to the Server

## Introduction

Now that we have secured access to our services, even for local access, let's deploy some services and applications that we can actually start configuring and using daily. Below, I will mention some of the applications I host on my homeserver with a brief description of their scope. Additionally, I will include some template compose files in the same directory named after their use case. The compose files will have port mappings but are intended only as references for adding your proxy hosts on NPM. We can delete the port mappings before even building the container for the first time, as long as we use the correct ports on our NPM proxy host. It is also recommended to test each connection and proper application behavior before moving to the next one.

## My Services Tour

Let's take a look at the services / applications I run on my home server and what their scope is:

### Portainer
- **One must have** in my opinion, especially when running services on a headless server. It provides us with a web UI for our Docker cluster, allowing us to do many things such as viewing containers logs for errors, changing their network, and easily updating our container images.

### Home Assistant OS (HAOS)
- Widely known open source application for Smart Home management. HAOS is so open-source and supported by the community that I consider it limitless. From power consumption monitoring to smart home sensor monitoring, or making custom automations more precise and powerful compared to any closed-source alternatives.

### My AI Services
- This template showcases four different applications:
 - **Ollamma**: A widely known application for hosting LLM models locally on the server.
 - **OpenWebUI**: A web chat interface to interact with LLMs.
 - **N8n**: Automation framework for LLMs.
 - **postgres**: Database container so we can feed the LLM with context.

### Grafana
- Grafana is a dashboard monitoring application with limitless flexibility of use case. It draws data from another service that deploys alongside named Prometheus. You can actually feed Prometheus with any information you want, using Prometheus export containers of other applications (usually distributed by the same maintainers as the application). With that, Grafana can display anything you want: power consumption of the home? Yes! Unsuccessful logins to services? Yes! All the databases' audit logs? Aha! Even health tracking data.

### Homarr
- No server with multiple services can be complete without an entry point dashboard. Meet Homarr, an open-source dashboard where you can customize it to your heart's content by adding widgets, clocks, calendars, email notifications, media control of your smart devices, and course the main point. Tiles with icons that redirect you to your services URLs. A gateway for your home server.

## Reflection

Here was my first attempt at setting up and starting multiple containers while isolating some of them using one compose file for my AI Services. Unfortunately, I cannot experiment with those applications a lot due to the hardware limitations of my server, but I plan to install them later on my personal laptop (using Docker) and play around there. 

In this setup, I also learned a lot about Docker networks. I created a new "secret" network bridge marked as isolated. What this means is that containers in that network are isolated from everything else, and normally we would have no way to access them from outside that network. In this secret network, I connected all four containers of my AI services since the Ollamma and the database container don't need to be accessed by anyone not even me. Then, I added OpenWebUI and N8n on the "exampleproxybridge" so NPM can reach them and resolve their addresses. This is a good security practice for containers that work in the background and don't require any access. 

I also learned that containers needing access to the Docker engine like Portainer to manage the cluster or Home Assistant to discover smart devices on the local network from the isolated bridge network require WebSocket enabled on their proxy host entry at NPM as well as a specific configuration line under volumes in their compose files. 

Grafana and Prometheus alongside with any other container needed to export data from services for Grafana to visualize is another good example of case where we can connected some of the containers to a completely isolated network and expose only the ones we have to access by connecting them to another network at the same time. 