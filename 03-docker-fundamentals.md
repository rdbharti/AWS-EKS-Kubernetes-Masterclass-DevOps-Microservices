# Docker Introduction
### What problems we have with Traditional Infrastructure?
- Needed to perform install/configs on every server/machine and every environment (dev/qa/staging/production)
- Issues related to libraries and dependecies keep arising and needed to be resolved. Which resulted in Compatibility and Dependency issues.
- Difficult to track changes across environments and they end-up with inconsistencies across environments.
- needed more resources to handle operational issues on day to day basis
    - server support
    - patching releases
- For new-developers it was time taking to provision his development environment in traditional approach.

### VMs vs Containers 
- Each containers has their own libraries, dependencies, image etc.

![Virtual Machine Vs Containers](https://k21academy.com/wp-content/uploads/2020/05/2020_05_13_12_19_07_PowerPoint_Slide_Show_Azure_AZ104_M01_Compute_ed1_-1536x701.png)

# Advantages of using Docker
- Flexible
    - Even the most complex applications can be containerazed
- LightWeight
    - Conatiners leverage and share the host kernal, making them more efficient in terms of system resources than virtual machines.
- Portable
    - You can build locally -> Deploy to Cloud -> Run anywhere
- Loosely Coupled
    - Containers are highly self suffcient and encapsulated, 
    allowing to replace/upgrade one without disrupting others.
- Scalable
    - Can increase and automatically distribute cconatiners replicas across a datacentre.
- Secure
    - Containers apply aggressive constrains and isolations to process without any configuration required on the part of the user.