В качестве базы данных использовать реляционную БД.

БД содержит три таблицы:
```
users (id, login)
teams (id, name)
users_teams (user_id, team_id)
```

Изначально таблицы БД должны иметь некоторое наполнение (см. код класса Demo).

В корне создать каталог sql и сохранить в нем скрипт создания базы данных `db-create.sql`

Создать и реализовать типы таким образом, чтобы при запуске класса Demo отрабатывала соответствующая функциональность.

--------------------------------------------------
Пакет: `com.epam.rd.java.basic.topic07.task02`
Классы: 
Demo - содержит демонстрацию функционала, оставить без изменений.
Остальные классы см. репозиторий.

--------------------------------------------------
Содержимое класса Demo:
```
public class Demo {

	public static void main(String[] args) throws DBException {
		// users  ==> [ivanov petrov obama]
		// teams  ==> [teamA teamB teamC ]
		
		DBManager dbManager = DBManager.getInstance();

		User userPetrov = dbManager.getUser("petrov");
		User userIvanov = dbManager.getUser("ivanov");
		User userObama = dbManager.getUser("obama");

		Team teamA = dbManager.getTeam("teamA");
		Team teamB = dbManager.getTeam("teamB");
		Team teamC = dbManager.getTeam("teamC");

		// method setTeamsForUser must implement transaction!
		dbManager.setTeamsForUser(userIvanov, teamA);
		dbManager.setTeamsForUser(userPetrov, teamA, teamB);
		dbManager.setTeamsForUser(userObama, teamA, teamB, teamC);

		for (User user : dbManager.findAllUsers()) {
			System.out.println((dbManager.getUserTeams(user)));
		}
		// teamA
		// teamA teamB
		// teamA teamB teamC
	}
}
```
(1) Метод DBManager#`setTeamsForUser(User, Team...)` реализовать с помощью транзакции: вследствие вызова данного метода пользователю будут назначены либо все группы, указаннные в списке аргументов, либо ни одна из них.

Если метод будет вызван так: `setTeamsForUser(user, teamA, teamB, teamC)`,
то в таблицу связей `users_teams` записи должны быть вставлены последовательно в порядке появления групп в списке аргументов слева направа:
```
user_id, teamA_id
user_id, teamB_id 
user_id, teamC_id
```
Если последняя запись не может быть добавлена, то первые две также не должны попасть в базу данных.

(2) Метод DBManager#`getUserTeams` возвращает объект `java.util.List<Team>`.

(3) Метод DBManager#`insertUser` должен модифицировать поле id объекта User.

(4) Метод DBManager#`findAllUsers` возвращает объект `java.util.List<User>`.

(5) Метод DBManager#`insertTeam` должен модифицировать поле id объекта Team.

(6) Метод DBManager#`findAllTeams` возвращает объект `java.util.List<Team>`.

##### Замечание.

Класс User должен содержать:
- метод `getLogin()`, который возвращает логин пользователя;
- метод `toString()`, который возвращает логин пользователя;
- реализацию метода `equals(Object obj)`, согласно которой два объекта User равны тогда и только тогда, когда они имеют один логин;
- статический метод `createUser(String login)`, который создает объект User по логину (идентификатор равен 0).

Класс Team должен содержать:
- метод `getName()`, который возвращает название группы;
- метод `toString()`, который возвращает название группы;
- реализацию метода `equals(Object obj)`, согласно которой два объекта Team равны тогда и только тогда, когда они имеют одно название.
- статический метод `createTeam(String name)`, который создает объект Team по имени (идентификатор равен 0).
