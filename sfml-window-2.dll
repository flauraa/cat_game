//libs
#include <SFML/Graphics.hpp>
#include <string>
#include <stdlib.h>
#include <ctime>

//constants
const unsigned short int windowWidth = 1600, windowHeight = 900;

//globals
sf::RenderWindow window;
sf::Event windowEvent;
sf::Clock frameClock;
float deltaTime;
sf::Font font;
sf::Text scoreText;

class Level
{
private:
	sf::RectangleShape background, ground;
	sf::Texture backgroundTexture, groundTexture;

public:
	Level(std::string backgroundTextureLocation, std::string groundTextureLocation)
	{
		background.setSize(sf::Vector2f(windowWidth, windowHeight));
		background.setPosition(0, 0);
		backgroundTexture.loadFromFile(backgroundTextureLocation);
		background.setTexture(&backgroundTexture);
		background.setFillColor(sf::Color(200, 200, 200));

		ground.setSize(sf::Vector2f(windowWidth, 150));
		ground.setPosition(0, windowHeight - 150);
		groundTexture.loadFromFile(groundTextureLocation);
		ground.setTexture(&groundTexture);
	}

	void draw()
	{
		window.draw(background);
		window.draw(ground);
	}
};

class
{
private:
	sf::RectangleShape cat;
	int catSize = 150;
	sf::Texture catTexture;
	std::string direction = "left";
	float speed = 400;
	int score = 0;

public:
	void init()
	{
		cat.setPosition(sf::Vector2f(750, windowHeight - 150 - catSize));
		cat.setSize(sf::Vector2f(catSize, catSize));
		catTexture.loadFromFile("assets/cat.png");
		cat.setTexture(&catTexture);
	}

	void draw()
	{
		if(direction == "left")
			cat.setTextureRect(sf::IntRect(0, 0, catTexture.getSize().x, catTexture.getSize().y));
		else 
			cat.setTextureRect(sf::IntRect(catTexture.getSize().x, 0, -catTexture.getSize().x, catTexture.getSize().y));

		window.draw(cat);
	}

	void move()
	{
        if(sf::Keyboard::isKeyPressed(sf::Keyboard::A))
        {
        	direction = "left";
            cat.move(sf::Vector2f(-speed * deltaTime, 0));
            if(cat.getPosition().x < 0)
            	cat.move(sf::Vector2f(speed * deltaTime, 0));
        }

        if(sf::Keyboard::isKeyPressed(sf::Keyboard::D))
        {
        	direction = "right";
            cat.move(sf::Vector2f(speed * deltaTime, 0));
            if(cat.getPosition().x > windowWidth - catSize)
            	cat.move(sf::Vector2f(-speed * deltaTime, 0));
        }
	}

	void increaseScore(int value)
	{
		score += value;
	}

	int getScore()
	{
		return score;
	}

	void resetScore()
	{
		score = 0;
	}

	sf::FloatRect getBounds()
	{
		return cat.getGlobalBounds();
	}

}cat;

sf::Texture fishTexture, heartTexture, bombTexture;
int ri = 2;
int fi = 1;

class Drop
{
protected:
	sf::RectangleShape object;
	int size = 85;
	float speed = 100;

public:
	void spawn()
	{	
		srand(time(0) / ri++ + clock() + fi);
		float posX = rand() % 1400 + 100;
		object.setPosition(sf::Vector2f(posX, -size));
	}

	Drop()
	{
		object.setSize(sf::Vector2f(size, size));
		spawn();
	}

	void draw()
	{
		window.draw(object);
	}

	bool collideWithCat()
	{
		return object.getGlobalBounds().intersects(cat.getBounds());
	}

	bool collideWithGround()
	{
		return !(object.getPosition().y <= windowHeight - 150 - size);
	}

	void move()
	{
		object.move(sf::Vector2f(0, speed * deltaTime));
	}

};

class Fish : public Drop
{
public:
	Fish() : Drop()
	{
		object.setTexture(&fishTexture);
	}
};

class Heart : public Drop
{
public:
	Heart() : Drop()
	{
		object.setTexture(&heartTexture);
	}
};

class Bomb : public Drop
{
public:
	Bomb() : Drop()
	{
		object.setTexture(&bombTexture);
	}
};

Level level1("assets/bk2.jpg", "assets/ground.png");
std::vector<Fish> fish;
std::vector<Heart> heart;
std::vector<Bomb> bomb;

void setup()
{
	font.loadFromFile("assets/font.ttf");
	scoreText.setPosition(sf::Vector2f(1350, 50));
	scoreText.setFont(font);
	scoreText.setCharacterSize(50);
	scoreText.setFillColor(sf::Color(230, 130, 70));
	window.create(sf::VideoMode(windowWidth, windowHeight), "joc tofu", sf::Style::Close);
	window.setFramerateLimit(100);
	cat.init();
	fishTexture.loadFromFile("assets/fish.png");
	heartTexture.loadFromFile("assets/heart.png");
	bombTexture.loadFromFile("assets/bomb.png");
}

int rvalue = 6; 

void update()
{
	srand(time(0) + rvalue + fi++);
	rvalue = rand() % 2000 + 1;
	if(rvalue >= 1 && rvalue <= 11)
	{
		fish.push_back(Fish());
	}
	else if(rvalue == 100 || rvalue == 75)
	{
		heart.push_back(Heart());
	}
	else if(rvalue == 55 || rvalue == 70 || rvalue == 90)
	{
		bomb.push_back(Bomb());
	}

	cat.move();

	for(int i = 0; i < fish.size(); i++)
	{
		fish[i].move();
		if(fish[i].collideWithGround())
		{
			fish.erase(fish.begin() + i);
		}
		else if(fish[i].collideWithCat())
		{
			cat.increaseScore(2);
			fish.erase(fish.begin() + i);
		}
	}

	for(int i = 0; i < heart.size(); i++)
	{
		heart[i].move();
		if(heart[i].collideWithGround())
		{
			heart.erase(heart.begin() + i);
		}
		else if(heart[i].collideWithCat())
		{
			cat.increaseScore(50);
			heart.erase(heart.begin() + i);
		}
	}

	for(int i = 0; i <bomb.size(); i++)
	{
		bomb[i].move();
		if(bomb[i].collideWithGround())
		{
			bomb.erase(bomb.begin() + i);
		}
		else if(bomb[i].collideWithCat())
		{
			cat.resetScore();
			bomb.erase(bomb.begin() + i);
		}
	}

	scoreText.setString("score: " + std::to_string(cat.getScore()));

	//render
	window.clear(sf::Color(150, 200, 250));
	level1.draw();

	for(int i = 0; i < fish.size(); i++)
	{
		fish[i].draw();
	}

	for(int i = 0; i < heart.size(); i++)
	{
		heart[i].draw();
	}

	for(int i = 0; i < bomb.size(); i++)
	{
		bomb[i].draw();
	}


	cat.draw();
	window.draw(scoreText);
	window.display();
}

int main()
{
    setup();
    while(window.isOpen())
    {
    	while(window.pollEvent(windowEvent))
        	if(windowEvent.type == sf::Event::Closed)
           		window.close();

        deltaTime = frameClock.restart().asSeconds();   	

        update();
    }
}
