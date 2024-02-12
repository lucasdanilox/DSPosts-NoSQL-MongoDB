# Workshop Spring Boot MongoDB DevSuperior

### Modelo conceitual

![Modelo de domínio ](https://i.postimg.cc/7hCS1n3w/README-DSPosts.png)

### Container Docker do MongoDB para desenvolvimento

```
docker run -d -p 27017:27017 -v /data/db --name mongo1 mongo:4.4.3-bionic
```

```
docker exec -it mongo1 bash
```

Seed

```java
userRepository.deleteAll();
postRepository.deleteAll();

User maria = new User(null, "Maria Brown", "maria@gmail.com");
User alex = new User(null, "Alex Green", "alex@gmail.com");
User bob = new User(null, "Bob Grey", "bob@gmail.com");

userRepository.saveAll(Arrays.asList(maria, alex, bob));

Post post1 = new Post(null, Instant.parse("2021-02-13T11:15:01Z"), "Partiu viagem", "Vou viajar para São Paulo. Abraços!", new Author(maria));
Post post2 = new Post(null, Instant.parse("2021-02-14T10:05:49Z"), "Bom dia", "Acordei feliz hoje!", new Author(maria));

Comment c1 = new Comment("Boa viagem mano!", Instant.parse("2021-02-13T14:30:01Z"), new Author(alex));
Comment c2 = new Comment("Aproveite", Instant.parse("2021-02-13T15:38:05Z"), new Author(bob));
Comment c3 = new Comment("Tenha um ótimo dia!", Instant.parse("2021-02-14T12:34:26Z"), new Author(alex));

post1.getComments().addAll(Arrays.asList(c1, c2));
post2.getComments().addAll(Arrays.asList(c3));

postRepository.saveAll(Arrays.asList(post1, post2));

maria.getPosts().addAll(Arrays.asList(post1, post2));
userRepository.save(maria);		
```

Consulta detalhada

```json
{ 
	$and: [ 
		{ 
			moment: {
				$gte: ?1
			} 
		}, 
		{ 	
			moment: { 
				$lte: ?2} 
		} , 
		{ 	
			$or: [ 
				{ 
					'title': { 
						$regex: ?0, 
						$options: 'i' 
					} 
				}, 
				{ 
					'body': { 
						$regex: ?0, 
						$options: 'i' 
					} 
				}, 
				{ 
					'comments.text': { 
						$regex: ?0, $options: 'i' 
					} 
				} 
			] 
		} 
	] 
}
