# Use official Nginx image
FROM nginx:alpine

# Remove the default Nginx web files
RUN rm -rf /usr/share/nginx/html/*

# Copy our static files into the container
COPY ../static-site /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start Nginx server
CMD ["nginx", "-g", "daemon off;"]
