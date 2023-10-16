import org.eclipse.jgit.api.Git;
import org.eclipse.jgit.diff.DiffEntry;
import org.eclipse.jgit.lib.Repository;
import org.eclipse.jgit.revwalk.RevCommit;
import org.eclipse.jgit.transport.URIish;

import java.io.File;
import java.io.IOException;
import java.net.URISyntaxException;
import java.util.List;

public class GitModifiedFiles {
    public static void main(String[] args) {
        String repositoryUrl = "https://github.com/yourusername/yourrepository.git"; // Replace with your Git repository URL

        try {
            Git git = Git.cloneRepository()
                    .setURI(repositoryUrl)
                    .setDirectory(new File("local-repo-directory"))
                    .call();

            Repository repository = git.getRepository();

            Iterable<RevCommit> commits = git.log().setMaxCount(1).call();
            for (RevCommit commit : commits) {
                List<DiffEntry> diffs = git.diff()
                        .setOldTree(commit.getTree())
                        .setNewTree(commit.getTree())
                        .call();

                for (DiffEntry diff : diffs) {
                    System.out.println("Modified File: " + diff.getNewPath());
                }
            }

            git.close();
        } catch (IOException | URISyntaxException e) {
            e.printStackTrace();
        }
    }
}
