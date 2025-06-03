# Use the official NGINX image as the base image
FROM nginx:alpine

# Copy custom configuration files
COPY default.conf /etc/nginx/conf.d/default.conf

# Expose port 80
EXPOSE 80

# Start NGINX
CMD ["nginx", "-g", "daemon off;"]
