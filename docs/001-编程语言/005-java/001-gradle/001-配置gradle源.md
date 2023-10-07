# 配置 gradle 源
在执行 **./gradlew build** 的时候失败，看起来是去请求国外的网站超时了，为了解决这个问题直接用国内的阿里云。

---

## 配置
gradle 的安装目录下有一个叫 **init.d** 的目录，我们要在这个目录下创建一个叫 **init.gradle** 的配置文件，它的内容如下
```gradle
gradle.projectsLoaded {
    rootProject.allprojects {
        buildscript {
            repositories {
                def JCENTER_URL = 'https://maven.aliyun.com/repository/public'
                def GOOGLE_URL = 'https://maven.aliyun.com/repository/google'
                def NEXUS_URL = 'https://maven.aliyun.com/repository/central'
                all { ArtifactRepository repo ->
                    if (repo instanceof MavenArtifactRepository) {
                        def url = repo.url.toString()
                        if (url.startsWith('https://jcenter.bintray.com/')) {
                            project.logger.lifecycle "Repository ${repo.url} replaced by $JCENTER_URL."
                            println("buildscript ${repo.url} replaced by $JCENTER_URL.")
                            remove repo
                        }
                        else if (url.startsWith('https://dl.google.com/dl/android/maven2/')) {
                            project.logger.lifecycle "Repository ${repo.url} replaced by $GOOGLE_URL."
                            println("buildscript ${repo.url} replaced by $GOOGLE_URL.")
                            remove repo
                        }
                        else if (url.startsWith('https://repo1.maven.org/maven2')) {
                            project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
                            println("buildscript ${repo.url} replaced by $REPOSITORY_URL.")
                            remove repo
                        }
                    }
                }
                jcenter {
                    url JCENTER_URL
                }
                google {
                    url GOOGLE_URL
                }
                maven {
                    url NEXUS_URL
                }
            }
        }
        repositories {
            def JCENTER_URL = 'https://maven.aliyun.com/repository/public'
            def GOOGLE_URL = 'https://maven.aliyun.com/repository/google'
            def NEXUS_URL = 'https://maven.aliyun.com/repository/central'
            all { ArtifactRepository repo ->
                if (repo instanceof MavenArtifactRepository) {
                    def url = repo.url.toString()
                    if (url.startsWith('https://jcenter.bintray.com/')) {
                        project.logger.lifecycle "Repository ${repo.url} replaced by $JCENTER_URL."
                        println("buildscript ${repo.url} replaced by $JCENTER_URL.")
                        remove repo
                    }
                    else if (url.startsWith('https://dl.google.com/dl/android/maven2/')) {
                        project.logger.lifecycle "Repository ${repo.url} replaced by $GOOGLE_URL."
                        println("buildscript ${repo.url} replaced by $GOOGLE_URL.")
                        remove repo
                    }
                    else if (url.startsWith('https://repo1.maven.org/maven2')) {
                        project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
                        println("buildscript ${repo.url} replaced by $REPOSITORY_URL.")
                        remove repo
                    }
                }
            }
            jcenter {
                url JCENTER_URL
            }
            google {
                url GOOGLE_URL
            }
            maven {
                url NEXUS_URL
            }
        }
    }
}
```
