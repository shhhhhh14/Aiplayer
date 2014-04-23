Aiplayer
========
//
// Team Name:Oasis
// Shauna Dowling, Ciarán Nolan, Eoghan McCarey
//

import java.util.ArrayList;


public class AiPlayer {

	private int playerId;
	private Board gameBoard;
	private Dice gameDice;
	
	
	AiPlayer (int setPlayerId, Board setBoard, Dice setDice) {
		playerId = setPlayerId;
		gameBoard = setBoard;
		gameDice = setDice;
	    return;
	}
	
	
	public int getPlayerId () {
		return playerId;
	}
	
	//when you blot an opposing player's checker 10 points are gained
	private boolean isHit(Board board) {
		boolean isBlot = false;
		int oppPlayer=gameBoard.opposingPlayer(playerId);
		if ( gameBoard.checkers[oppPlayer][Board.BAR] < board.checkers[oppPlayer][Board.BAR] )
			isBlot=true;
		return isBlot;
	}
	
	//when all checkers are beared off ten point are gained
	private boolean isBearOff(Board board) {
		boolean bearOff=false;
		
		if (gameBoard.checkers[playerId][Board.OFF]<board.checkers[playerId][Board.OFF])
			bearOff=true;
		return bearOff;
	}
	
	//if you make a move which allows a checker to be on a pip on their own two points are lost
	private boolean chippyNoMates(Board board) {
		boolean hasNoMates=false;
		
		for (int i=1; i<Board.NUM_PIPS-1; i++) {
			if (gameBoard.checkers[playerId][i]!=1 && board.checkers[playerId][i]==1)
				hasNoMates=true;
			if (hasNoMates)
				break;
		}
		return hasNoMates;
	}
	
	//if you reinforce checker which was their own you gain two points
	private boolean reinforce(Board board){
		boolean reinforce=false;
		
		for (int i=1; i<Board.NUM_PIPS-1; i++){
			if(gameBoard.checkers[playerId][i]==1 && board.checkers[playerId][i]>=2)
				reinforce=true;
			if(reinforce)
				break;
		}
		return reinforce;
	}
	
	//if all checkers are brought onto the home board the player gets another 5 points
	private boolean allHome(Board board){
		boolean allHome=false;
		
		for(int i=1; i<Board.NUM_PIPS; i++){
			if(gameBoard.checkers[playerId][i]==26 && board.checkers[playerId][i]<=7)
				allHome=true;
			if(allHome)
				break;
		}
		return allHome;
	}
	
	//score generator which allocates points in accordance with moves made 
	private int generateScore(Board board) {
		int score=0;
		if (isHit(board)) score+=10;
		if (isBearOff(board)) score+=10;
		if (chippyNoMates(board)) score-=2;
		if (reinforce(board)) score+=2;
		if(allHome(board)) score+=5;
		
		return score;
		
	}
	
	private int findBestBoard (ArrayList<Board> allBoardsList) {
		int bestBoard = 0;
		int currentScore=0;
		int bestScore=0;
		for (int i=0; i<allBoardsList.size(); i++) {
			currentScore=generateScore(allBoardsList.get(i));
			if ( currentScore > bestScore ) {
				bestScore=currentScore;
				bestBoard=i;
			}
		}
		return bestBoard;
	}
	
	
	public Play getPlay () {
		ArrayList<Play> allPlayList;
		ArrayList<Board> allBoardsList = new ArrayList<Board>();
		int bestBoard;
		Play chosenPlay;
		
		allPlayList = gameBoard.allPossiblePlays (playerId, gameDice);
		if (!allPlayList.isEmpty()) {
			for (int i=0; i<allPlayList.size(); i++) {
				allBoardsList.add(new Board(gameBoard));
				allBoardsList.get(i).doPlay(playerId, allPlayList.get(i));
			}
			bestBoard = findBestBoard(allBoardsList);
			chosenPlay = allPlayList.get(bestBoard);
		}
		else {
			chosenPlay = new Play();
		}
		
		return chosenPlay;
	}
		
	
}
