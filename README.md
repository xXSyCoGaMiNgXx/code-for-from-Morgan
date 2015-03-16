# code-for-from-Morgan
code cut short from Morgan
package uk.ac.reading.sis05kol.mooc;

//Other parts of the android libraries that we use
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;

public class TheGame extends GameThread{

    //Will store the image of a ball
    private Bitmap mBall;

    //The X and Y position of the ball on the screen (middle of ball)
    private float mBallX = -100;
    private float mBallY = -100;

    //The speed (pixel/second) of the ball in direction X and Y
    private float mBallSpeedX = 0;
    private float mBallSpeedY = 0;

    private Bitmap mPaddle;

    private float mPaddleX = 0;
    private float mPaddleSpeedX = 0;



    private  float mMinDistanceBetweenBallAndPaddle;

    private Bitmap mSmileyBall;

    private float mSmileyBallX = 0;

    //This is run before anything else, so we can prepare things here
    public TheGame(GameView gameView) {
        //House keeping
        super(gameView);

        //Prepare the image so we can draw it on the screen (using a canvas)
        mBall = BitmapFactory.decodeResource
                (gameView.getContext().getResources(),
                        R.drawable.small_red_ball);

        mPaddle = BitmapFactory.decodeResource
                (gameView.getContext().getResources(),
                        R.drawable.yellow_ball);


        mSmileyBall = BitmapFactory.decodeResource
                (gameView.getContext().getResources(),
                        R.drawable.smiley_ball);

    }

    //This is run before a new game (also after an old game)
    @Override
    public void setupBeginning() {
        //Initialise speeds
        mBallSpeedX = mCanvasHeight / 3;
        mBallSpeedY = mCanvasWidth / 3;

        //Place the ball in the middle of the screen.
        //mBall.Width() and mBall.getHeigh() gives us the height and width of the image of the ball
        mBallX = mCanvasWidth / 2;
        mBallY = mCanvasHeight / 2;

        mPaddleX = mCanvasWidth / 2;
        mPaddleSpeedX = 0;




        mMinDistanceBetweenBallAndPaddle = (mPaddle.getWidth() /2 + mBall.getWidth() /2) * (mPaddle.getWidth() /2 + mBall.getWidth() /2);




}

    @Override
    protected void doDraw(Canvas canvas) {
        //If there isn't a canvas to draw on do nothing
        //It is ok not understanding what is happening here
        if(canvas == null) return;

        super.doDraw(canvas);

        //draw the image of the ball using the X and Y of the ball
        //drawBitmap uses top left corner as reference, we use middle of picture
        //null means that we will use the image without any extra features (called Paint)
        canvas.drawBitmap(mBall, mBallX - mBall.getWidth() / 2, mBallY - mBall.getHeight() / 2, null);

        canvas.drawBitmap(mPaddle, mPaddleX - mPaddle.getWidth() / 2, mCanvasHeight - mPaddle.getHeight()/2, null);


        canvas.drawBitmap(mSmileyBall, mSmileyBallX - mSmileyBall.getWidth() / 2 +
                mCanvasWidth / 2,50, null);






    }

    //This is run whenever the phone is touched by the user

	@Override
	protected void actionOnTouch(float x, float y) {
		//Increase/decrease the speed of the ball making the ball move towards the touch
        mPaddleX = x;
	}



	//This is run whenever the phone moves around its axises
	@Override
	protected void actionWhenPhoneMoved(float xDirection, float yDirection, float zDirection) {
		/*
		Increase/decrease the speed of the ball.
		If the ball moves too fast try and decrease 70f
		If the ball moves too slow try and increase 70f
		 */

		mBallSpeedX = mBallSpeedX + 70f * xDirection;
		mBallSpeedY = mBallSpeedY - 70f * yDirection;

        mPaddleSpeedX = mPaddleSpeedX + 70f * xDirection;

        if (mPaddleX <= 0 && mPaddleSpeedX < 0){
            mPaddleSpeedX = 0;
            mPaddleX = 0;
        }
        if (mPaddleX >= mCanvasWidth && mPaddleSpeedX > 0) {
            mPaddleSpeedX = 0;
            mPaddleX = mCanvasWidth;
        }
	}


    //This is run just before the game "scenario" is printed on the screen
    @Override
    protected void updateGame(float secondsElapsed) {
        float DistanceBetweenBallAndPaddle;



        if (mBallSpeedY > 0) {
            DistanceBetweenBallAndPaddle = (mPaddleX - mBallX) * (mPaddleX - mBallX) + (mCanvasHeight) - (mBallY) * (mCanvasHeight) - (mBallY);

            if (mMinDistanceBetweenBallAndPaddle >= DistanceBetweenBallAndPaddle){
               float speedOfBall = (float) Math.sqrt(mBallSpeedX*mBallSpeedX + mBallSpeedY*mBallSpeedY);


                mBallSpeedX = mBallX - mPaddleX;
                mBallSpeedY = mBallY - mCanvasHeight;

                float newSpeedOfBall = (float) Math.sqrt(mBallSpeedX*mBallSpeedX + mBallSpeedY*mBallSpeedY);

                mBallSpeedX = mBallSpeedX * speedOfBall / newSpeedOfBall;
                mBallSpeedY = mBallSpeedY * speedOfBall / newSpeedOfBall;

                updateScore(1);
            }
        }
        //Move the ball's X and Y using the speed (pixel/sec)
        mBallX = mBallX + secondsElapsed * mBallSpeedX;
        mBallY = mBallY + secondsElapsed * mBallSpeedY;

        if(mBallX <= mBall.getWidth()/2 && mBallSpeedX < 0)
            mBallSpeedX = -mBallSpeedX;


        if((mBallX >= mCanvasWidth - mBall.getWidth()/2 && mBallSpeedX > 0))
            mBallSpeedX = -mBallSpeedX;




        if(mBallY <= mBall.getWidth()/2 && mBallSpeedY < 0)
            mBallSpeedY = -mBallSpeedY;








         mPaddleX = mPaddleX + secondsElapsed * mPaddleSpeedX;


        if (mBallY >= mCanvasHeight) {
            setState(GameThread.STATE_LOSE);
        }








    }
    }





// This file is part of the course "Begin Programming: Build your first mobile game" from futurelearn.com
// Copyright: University of Reading and Karsten Lundqvist
// It is free software: you can redistribute it and/or modify
// it under the terms of the GNU General Public License as published by
// the Free Software Foundation, either version 3 of the License, or
// (at your option) any later version.
//
// It is is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
//
//
// You should have received a copy of the GNU General Public License
// along with it.  If not, see <http://www.gnu.org/licenses/>.
