CREATE TABLE lok_warframes(
  id INT AUTO_INCREMENT NOT NULL PRIMARY KEY ,
  name VARCHAR(15) NOT NULL,
  health SMALLINT,
  shield SMALLINT,
  armor SMALLINT,
  abilitiesID INT,
  sprintSpeed SMALLINT,
  mainPolarity INT,
  CONSTRAINT FK_warframeAbilities FOREIGN KEY (abilitiesID)
    REFERENCES lok_abilities(id),
  CONSTRAINT FK_warframeMainPolarity FOREIGN KEY (mainPolarity)
    REFERENCES lok_polarities(id)
);
CREATE INDEX warframeName ON lok_warframes(name);
CREATE INDEX nafnÁIndexi ON töfluNafn (nafnÁDálki);

drop table lok_warframes;

CREATE TABLE lok_abilities(
  id INT AUTO_INCREMENT NOT NULL PRIMARY KEY ,
  name VARCHAR(20) UNIQUE ,
  description TEXT
);

CREATE INDEX abilityName ON lok_abilities(name);

CREATE TABLE lok_polarities(
  id INT AUTO_INCREMENT NOT NULL PRIMARY KEY ,
  name VARCHAR(20) UNIQUE ,
  DESCRIPTION TEXT
);
CREATE TABLE lok_warframePolarity(
  polarity INT NOT NULL  ,
  warframeID INT NOT NULL ,
  PRIMARY KEY (polarity,warframeID),
  CONSTRAINT FK_warframePolarity FOREIGN KEY (polarity)
    REFERENCES lok_polarities(id),
  CONSTRAINT FK_warframeID FOREIGN KEY (warframeID)
    REFERENCES lok_warframes(id)
);
CREATE TABLE lok_weapons(
  id INT NOT NULL AUTO_INCREMENT PRIMARY KEY ,
  name VARCHAR(20) UNIQUE ,
  triggerType ENUM("semi-auto","full-auto","burst","auto"),
  flightSpeed SMALLINT,
  noiseLevel ENUM("silent","alarming"),
  fireRate SMALLINT,
  accuracy SMALLINT,
  magSize SMALLINT,
  maxAmmo SMALLINT,
  reloadTime SMALLINT,
  disposition ENUM("competent","strong","neutral","mild","faint"),
  impact SMALLINT,
  slash SMALLINT,
  puncture SMALLINT,
  critChance SMALLINT,
  critMultiplier SMALLINT,
  statusChance SMALLINT
);

ALTER TABLE lok_weapons
    ALTER impact
CREATE TABLE lok_weaponPolarities(
  weaponID INT NOT NULL ,
  polarityID INT NOT NULL ,
  PRIMARY KEY(weaponID,polarityID),
  CONSTRAINT FK_weaponID FOREIGN KEY (weaponID)
    REFERENCES lok_weapons(id),
  CONSTRAINT FK_polarityID FOREIGN KEY (polarityID)
    REFERENCES lok_polarities(id)
);
CREATE TABLE lok_mods(
  id INT NOT NULL AUTO_INCREMENT PRIMARY KEY ,
  name VARCHAR(20) UNIQUE ,
  type ENUM ("mod","auraMod","stance"),
  description TEXT
);
CREATE TABLE lok_modPolarities(
  modId INT NOT NULL ,
  polarityID INT NOT NULL ,
  CONSTRAINT FK_mp_modID FOREIGN KEY (modId)
    REFERENCES lok_mods(id),
  CONSTRAINT FK_mp_polarityID FOREIGN KEY (polarityID)
    REFERENCES lok_polarities(id),
  PRIMARY KEY (modID,polarityID)
);
CREATE TABLE lok_currencies(
  id INT AUTO_INCREMENT NOT NULL PRIMARY KEY ,
  name VARCHAR(10) UNIQUE ,
  description TEXT
);
CREATE INDEX currencyName ON lok_currencies(name);
CREATE TABLE lok_blueprints(
  id INT NOT NULL AUTO_INCREMENT PRIMARY KEY ,
  name VARCHAR(30),
  description TEXT
);
CREATE INDEX blueprintName ON lok_blueprints(name);

CREATE TABLE lok_resources(
  id INT NOT NULL AUTO_INCREMENT PRIMARY KEY ,
  type ENUM("fish","minerals","resources"),
  rarity ENUM("rare","common","uncommon"),
  name VARCHAR(20) UNIQUE
);

CREATE TABLE lok_equipments(
  id INT NOT NULL AUTO_INCREMENT,
  type ENUM("rifle","sentinel","kavat","pistol","bow","warframe",
  "archwing","shotgun","currency","archwing weapon"),
  equipID INT,
  PRIMARY KEY (id,equipID),

  CONSTRAINT FK_E_weaponID FOREIGN KEY (equipID)
    REFERENCES lok_weapons(id),
  CONSTRAINT FK_E_archwingID FOREIGN KEY (equipID)
    REFERENCES lok_archwings(id),
  CONSTRAINT FK_E_warframeID FOREIGN KEY (equipID)
    REFERENCES lok_warframes(id)

);
CREATE TABLE lok_factions(
  id INT NOT NULL AUTO_INCREMENT PRIMARY KEY ,
  name VARCHAR(20) UNIQUE ,
  description TEXT
);
CREATE TABLE lok_archwings(
  id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(20) UNIQUE,
  shield SMALLINT,
  health SMALLINT,
  power SMALLINT,
  flightSpeed SMALLINT,
  conclaveRating TINYINT
);

CREATE TABLE lok_unequippables(
  id INT NOT NULL AUTO_INCREMENT PRIMARY KEY ,
  type ENUM("currency","blueprint","mod","resource"),
  itemID INT,
  CONSTRAINT FK_UE_resourceID FOREIGN KEY(itemID)
    REFERENCES lok_resources(id),
  CONSTRAINT FK_UE_blueprintID FOREIGN KEY(itemID)
    REFERENCES lok_blueprints(id),
  CONSTRAINT FK_UE_currencyID FOREIGN KEY(itemID)
    REFERENCES lok_currencies(id),
  CONSTRAINT FK_UE_modID FOREIGN KEY(itemID)
    REFERENCES lok_mods(id)
);
CREATE TABLE lok_archwingPolarities(
  idArchwing INT,
  idPolarity INT,
  PRIMARY KEY (idArchwing,idPolarity),
  CONSTRAINT FK_AP_archwingID FOREIGN KEY (idArchwing)
    REFERENCES lok_archwings(id),
  CONSTRAINT FK_AP_polarityID FOREIGN KEY (idPolarity)
    REFERENCES lok_polarities(id)
);

CREATE TABLE lok_enemyTypesInFactions(
  enemyID INT NOT NULL,
  factionID INT NOT NULL ,
  PRIMARY KEY (enemyID,factionID),
  CONSTRAINT FK_ETIF_enemyID FOREIGN KEY (enemyID)
    REFERENCES lok_factions(id),
  CONSTRAINT FK_ETIF_factionID FOREIGN KEY (factionID)
    REFERENCES lok_enemyTypes(id)
);

CREATE TABLE lok_enemyTypes(
  id INT AUTO_INCREMENT NOT NULL PRIMARY KEY ,
  weapons INT NOT NULL,
  name VARCHAR(20) NOT NULL ,
  planet VARCHAR(15),
  type ENUM("ranged","melee","knockdown"),
  fleshType VARCHAR(20),
  armorType VARCHAR(20),
  bodyMultiplier VARCHAR(20),
  baseAffinity INT,
  baseLevel INT,
  codexScans INT
);
DROP TABLE lok_enemyTypes;
CREATE TABLE lok_enemyWeapons(
  enemyID INT NOT NULL ,
  weaponID INT NOT NULL ,
  PRIMARY KEY (enemyID,weaponID),
  CONSTRAINT FK_EW_enemyID FOREIGN KEY (enemyID)
    REFERENCES lok_enemyTypes(id),
  CONSTRAINT FK_EW_weaponID FOREIGN KEY (weaponID)
    REFERENCES lok_equipments(id)
);

CREATE TABLE lok_player(
  id INT AUTO_INCREMENT NOT NULL PRIMARY KEY  ,
  username VARCHAR(200),
  masteryRank VARCHAR(20)
);
CREATE INDEX IDX_username ON lok_player(username);
CREATE TABLE lok_playerInventories(

  idPlayer INT ,
  idItem INT,
  PRIMARY KEY (idPlayer,idItem)
);

DROP TABLE lok_playerInventories;
CREATE TABLE lok_clanVaults(
  clanID INT,
  itemID INT,
  PRIMARY KEY (clanID,itemID),
  CONSTRAINT FK_CV_clanID FOREIGN KEY (clanID)
    REFERENCES lok_clans(id),
  CONSTRAINT FK_CV_itemID FOREIGN KEY (itemID)
    REFERENCES lok_unequippables(id)
);


CREATE TABLE lok_clans(
  id  INT NOT NULL AUTO_INCREMENT PRIMARY KEY ,
  name VARCHAR(200),
  clanTier ENUM("ghost","shadow","storm","mountain","moon")
);

CREATE TABLE lok_clanVaults2(
  clanId INT,
  itemID INT,
  PRIMARY KEY (clanId,itemID),
  CONSTRAINT FK_CV2_clan FOREIGN KEY (clanId)
    REFERENCES lok_clans(id),
  CONSTRAINT FK_CV2_item FOREIGN KEY (itemID)
    REFERENCES lok_unequippables(id)
);
CREATE TABLE lok_warframeAbilities(
  idWarframe INT NOT NULL,
  idAbility INT NOT NULL ,
  PRIMARY KEY(idWarframe,idAbility),
  CONSTRAINT  FK_WA_idWarframe FOREIGN KEY(idWarframe)
    REFERENCES lok_warframes(id),
  CONSTRAINT FK_WA_idAbility FOREIGN KEY (idAbility)
    REFERENCES lok_abilities(id)
);
DELIMITER KKK
CREATE TRIGGER lok_newPlayer
    AFTER INSERT ON lok_player FOR EACH ROW
  BEGIN
    INSERT INTO lok_playerInventories (idPlayer, idItem) VALUES(new.id,1),
    (new.id,2),
    (new.id,3);

  END KKK
DELIMITER ;
DROP TRIGGER lok_newPlayer;

CREATE TABLE lok_missions(
  id INT AUTO_INCREMENT NOT NULL PRIMARY KEY ,
  name VARCHAR(20) NOT NULL ,
  type ENUM("assassionation","defense","capture","assault","exterminate","hijack","junction","arena","defection") NOT NULL ,
  planetID INT NOT NULL
);
DROP TABLE lok_missions;
CREATE TABLE lok_planets(
  id INT AUTO_INCREMENT NOT NULL PRIMARY KEY ,
  name VARCHAR(20),
  rulingFaction INT
);
CREATE TABLE lok_alertMissions(
  id INT NOT NULL  AUTO_INCREMENT PRIMARY KEY ,
  name VARCHAR(20),
  level INT,
    type ENUM("assassionation","defense","capture","assault","exterminate","hijack","junction","arena","defection") NOT NULL ,

  planetID INT NOT NULL
);
DROP TABLE lok_alertMissions;

DELIMITER KKK
CREATE EVENT lok_alert
  ON SCHEDULE EVERY 1 HOUR
  STARTS CURRENT_TIMESTAMP DO
  INSERT INTO lok_activeAlert VALUES

    INSERT INTO lok_player(emilorn.lok_player.id,emilorn.lok_player.masteryRank) VALUES (new.id,0);


