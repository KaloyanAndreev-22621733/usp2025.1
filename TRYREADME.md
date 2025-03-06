public classs Myclass 
{
import com.github.dockerjava.api.DockerClient;
import com.github.dockerjava.api.DockerClientBuilder;
import com.github.dockerjava.api.command.CreateContainerResponse;
import com.github.dockerjava.api.model.PullResponseItem;

public class DockerContainerInitializer {

    public static void main(String[] args) {
        // Initialize Docker client
        DockerClient dockerClient = DockerClientBuilder.getInstance().build();

        String imageName = "alpine:latest"; // Примерно избрано изображение
        String containerName = "my-alpine-container";

        try {
            // 1. Pull the image
            System.out.println("Pulling image: " + imageName);
            dockerClient.pullImageCmd(imageName).exec(new PullImageResultCallback()).awaitCompletion();

            // 2. Create a container from the image
            System.out.println("Creating container...");
            CreateContainerResponse container = dockerClient.createContainerCmd(imageName)
                    .withName(containerName)
                    .exec();

            System.out.println("Container created with ID: " + container.getId());

            // 3. Start the container
            System.out.println("Starting container...");
            dockerClient.startContainerCmd(container.getId()).exec();

            System.out.println("Container " + containerName + " is now running.");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            dockerClient.close();
        }
    }
    
    // Callback class for pulling images
    static class PullImageResultCallback extends com.github.dockerjava.core.command.PullImageResultCallback {
        @Override
        public void onNext(PullResponseItem item) {
            if (item.getStatus() != null) {
                System.out.println(item.getStatus());
            }
            super.onNext(item);
        }
    }
}
}