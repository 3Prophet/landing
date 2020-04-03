FROM openjdk:8
RUN mkdir /filestorage
RUN echo "This is the file storge" > /filestorage/greeting.txt
VOLUME /filestorage
EXPOSE 8013
ADD maven/landing-*.jar landing.jar
RUN sh -c "touch /landing.jar"
ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/.urandom","-jar","/landing.jar"]