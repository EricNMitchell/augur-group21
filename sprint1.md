#### Sprint 1 MD

---

##### Deployment Environment:
http://ec2-34-238-154-101.compute-1.amazonaws.com:3333


##### Functional Requirements:
* Use Case 1: User views which organizations are the top contributors to the project.

System must access project data from database
System will display the top 5 organizations by number of commits by default
System will give user the option to change display between top contributor of commits, comments, and merges. User can select an option to display this data.
	
* Use Case 2: User views which repos are the most popular (bookmarked) within a project.

System must access project data from database.
System will display the top 3 repos that have most stars in an account or a group (set of accounts under a specific name) in descending order

* Use Case 3: User views the newest projects.

System must access project data from database.
System presents a list of projects with start dates within the last 12 months based on the timestamp of the first commit

* Use Case 4: User views repos with recent commits.

System must access project data from database.
System displays a list of the repos that have had commits with timestamps within the last 30 days sorted in descending order of latest commit.


##### ERD:

![alt text](https://github.com/EricNMitchell/augur-group21/blob/master/schema.png "Schema ERD")


##### DDL:

```
CREATE TABLE `affiliations` (
`id` int(10) UNSIGNED NOT NULL AUTO_INCREMENT,
`domain` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL,
`affiliation` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL,
`start_date` date NOT NULL DEFAULT '1970-01-01',
`active` tinyint(1) NOT NULL DEFAULT 1,
`last_modified` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP(6) ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`id`) ,
UNIQUE INDEX `domain,affiliation,start_date` (`domain` ASC, `affiliation` ASC, `start_date` ASC),
INDEX `domain,active` (`domain` ASC, `active` ASC)
)
ENGINE = InnoDB
AUTO_INCREMENT = 522
DEFAULT CHARACTER SET = utf8mb4
COLLATE = utf8mb4_unicode_ci
ROW_FORMAT = compact;

CREATE TABLE `projects` (
`id` int(10) UNSIGNED NOT NULL AUTO_INCREMENT,
`name` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL,
`description` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NULL DEFAULT NULL,
`website` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NULL DEFAULT NULL,
`recache` tinyint(1) NULL DEFAULT 1,
`last_modified` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP(6) ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`id`) 
)
ENGINE = InnoDB
AUTO_INCREMENT = 7
DEFAULT CHARACTER SET = utf8mb4
COLLATE = utf8mb4_unicode_ci
ROW_FORMAT = compact;

CREATE TABLE `repos` (
`id` int(10) UNSIGNED NOT NULL AUTO_INCREMENT,
`projects_id` int(10) UNSIGNED NOT NULL,
`git` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL,
`path` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NULL DEFAULT NULL,
`name` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NULL DEFAULT NULL,
`added` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP(6),
`status` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL,
PRIMARY KEY (`id`) 
)
ENGINE = InnoDB
AUTO_INCREMENT = 140
DEFAULT CHARACTER SET = utf8mb4
COLLATE = utf8mb4_unicode_ci
ROW_FORMAT = compact;

CREATE TABLE `commits` (
`id` int(11) NOT NULL AUTO_INCREMENT,
`sha` varchar(40) NULL DEFAULT NULL,
`author_id` int(11) NULL DEFAULT NULL,
`committer_id` int(11) NULL DEFAULT NULL,
`project_id` int(11) NULL DEFAULT NULL,
`created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
PRIMARY KEY (`id`) ,
UNIQUE INDEX `sha` (`sha` ASC),
INDEX `commits_ibfk_1` (`author_id` ASC),
INDEX `commits_ibfk_2` (`committer_id` ASC),
INDEX `commits_ibfk_3` (`project_id` ASC)
)
ENGINE = MyISAM
AUTO_INCREMENT = 1182678117
DEFAULT CHARACTER SET = utf8
COLLATE = utf8_general_ci
ROW_FORMAT = dynamic;

CREATE TABLE `organization_members` (
`org_id` int(11) NOT NULL,
`user_id` int(11) NOT NULL,
`created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
PRIMARY KEY (`org_id`, `user_id`) ,
INDEX `organization_members_ibfk_2` (`user_id` ASC)
)
ENGINE = MyISAM
AUTO_INCREMENT = 0
DEFAULT CHARACTER SET = utf8
COLLATE = utf8_general_ci
ROW_FORMAT = fixed;

CREATE TABLE `project_commits` (
`project_id` int(11) NOT NULL DEFAULT 0,
`commit_id` int(11) NOT NULL DEFAULT 0,
INDEX `project_commits_ibfk_1` (`project_id` ASC),
INDEX `commit_id` (`commit_id` ASC)
)
ENGINE = MyISAM
AUTO_INCREMENT = 0
DEFAULT CHARACTER SET = utf8
COLLATE = utf8_general_ci
ROW_FORMAT = fixed;

CREATE TABLE `users` (
`id` int(11) NOT NULL AUTO_INCREMENT,
`login` varchar(255) NOT NULL,
`company` varchar(255) NULL DEFAULT NULL,
`created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
`type` varchar(255) NOT NULL DEFAULT 'USR',
`fake` tinyint(1) NOT NULL DEFAULT 0,
`deleted` tinyint(1) NOT NULL DEFAULT 0,
`long` decimal(11,8) NULL DEFAULT NULL,
`lat` decimal(10,8) NULL DEFAULT NULL,
`country_code` char(3) NULL DEFAULT NULL,
`state` varchar(255) NULL DEFAULT NULL,
`city` varchar(255) NULL DEFAULT NULL,
`location` varchar(255) NULL DEFAULT NULL,
PRIMARY KEY (`id`) ,
UNIQUE INDEX `login` (`login` ASC)
)
ENGINE = MyISAM
AUTO_INCREMENT = 47149986
DEFAULT CHARACTER SET = utf8
COLLATE = utf8_general_ci
ROW_FORMAT = dynamic;

CREATE TABLE `stargazers` (
`repo_id` int(11) NOT NULL,
`user_id` int(11) NOT NULL,
`created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
PRIMARY KEY (`repo_id`, `user_id`) ,
INDEX `watchers_ibfk_2` (`user_id` ASC)
)
ENGINE = MyISAM
AUTO_INCREMENT = 0
DEFAULT CHARACTER SET = utf8
COLLATE = utf8_general_ci
ROW_FORMAT = fixed;

ALTER TABLE `projects` ADD CONSTRAINT `fk_projects_repos_1` FOREIGN KEY (`id`) REFERENCES `repos` (`projects_id`);
ALTER TABLE `stargazers` ADD CONSTRAINT `fk_stargazers_repos_1` FOREIGN KEY (`repo_id`) REFERENCES `repos` (`id`);
ALTER TABLE `projects` ADD CONSTRAINT `fk_projects_project_commits_1` FOREIGN KEY (`id`) REFERENCES `project_commits` (`project_id`);
ALTER TABLE `project_commits` ADD CONSTRAINT `fk_project_commits_commits_1` FOREIGN KEY (`commit_id`) REFERENCES `commits` (`id`);
ALTER TABLE `commits` ADD CONSTRAINT `fk_commits_users_1` FOREIGN KEY (`author_id`) REFERENCES `users` (`id`);
ALTER TABLE `users` ADD CONSTRAINT `fk_users_organization_members_1` FOREIGN KEY (`id`) REFERENCES `organization_members` (`user_id`);
ALTER TABLE `organization_members` ADD CONSTRAINT `fk_organization_members_affiliations_1` FOREIGN KEY (`org_id`) REFERENCES `affiliations` (`id`);
```


##### Files stubbed out: Stargazers plugin (Use Case 2)
* User Interface Files

frontend/app/AugurAPI.js
// get data from the Stargazers.py backend
frontend/app.Augur.js
// display repo star count on the UI
	
* Model Files (Database Access)

augur/plugins/stargazers/routes.py
// connect to the github api to gather stargazers data
	
* Controller Files (API or other)

augur/plugins/stargazers/__init__.py
#SPDX-License-Identifier: MIT
from augur.application import Application
from augur.augurplugin import AugurPlugin
from augur import logger
class StargazersPlugin(AugurPlugin):
def __call__(self):
def create_routes(self, flask_app):
Stargazers.augur_plugin_meta = {}
augur/plugins/stargazers/Stargazers.py
def __init__(self):
// init the class
def get_stargazers
// get stargazer data from the github api and save to database			


##### Languages used and skill gaps:

Python
Javascript

Skill gaps:
Eric: Python
Tim: Python Javascript
Shengfeng: Javascript



