// This script is brought to by ROAST Labs (https://weareroast.com/roast-labs/) the internal development and innovation team at ROAST a digital marketing agency.
// The script turns on and off ad groups on GoogleAds based on the England football score

function main() {
  var startTime = new Date().getTime();
  var maxRunTime = 29 * 60 * 1000; // 29 minutes in milliseconds to stay within limits

  while (new Date().getTime() - startTime < maxRunTime) {
    runScript();
    Utilities.sleep(10000); // Sleep for 10 seconds between each run to avoid hitting rate limits
  }
}

function runScript() {
  
  // head to https://www.uefa.com/euro2024/fixtures-results/#/d/2024-06-14 and click on a match, grab the id from the URL and replace the matchID below 
  // for example Englands opening game is https://www.uefa.com/euro2024/match/2036166--serbia-vs-england/ so the matchID is 2036166

  var url = 'https://match.uefa.com/v5/matches?matchId=2040578';



  try {
    var response = UrlFetchApp.fetch(url);
    var data = JSON.parse(response.getContentText());
    
    if (data && data[0] && data[0].score) {
      var status = data[0].status;
      
      if (status === "FINISHED") {
        Logger.log('The match is finished. No changes will be made.');
        return;
      }

      if (status === "UPCOMING") {
        Logger.log('The match is yet to start. No changes will be made.');
        return;
      }

      var homeTeamCode = data[0].homeTeam.teamCode;
      var awayTeamCode = data[0].awayTeam.teamCode;
      var homeScore = data[0].score.regular.home;
      var awayScore = data[0].score.regular.away;

      // Determine if England is the home or away team, you can change the code to a different team, see the code in the URL above on line 16
      var englandIsHome = homeTeamCode === 'ENG';
      var englandIsAway = awayTeamCode === 'ENG';

      // Determine ad group states based on score and whether England is home or away
      var adGrpStates = {
        'EnglandWinning': (englandIsHome && homeScore > awayScore) || (englandIsAway && awayScore > homeScore),
        'EnglandLosing': (englandIsHome && homeScore < awayScore) || (englandIsAway && awayScore < homeScore),
        'EnglandDrawing': homeScore === awayScore
      };

      // Enter your campaign id below 
      var campaignId = '12345678910';

      // Function to update ad group state
      function updateAdGroupState(adGroupName, adGroupId, enable) {
        var adGroupIterator = AdWordsApp.adGroups()
          .withCondition("CampaignId = " + campaignId + " AND Id = " + adGroupId)
          .get();
        
        if (adGroupIterator.hasNext()) {
          var adGroup = adGroupIterator.next();
          if (enable) {
            adGroup.enable();
            Logger.log('Enabled ad group: ' + adGroupName);
          } else {
            adGroup.pause();
            Logger.log('Paused ad group: ' + adGroupName);
          }
        } else {
          Logger.log('Ad group not found: ' + adGroupName);
        }
      }

      // Update ad groups based on states, the number is the AdGroup ID
      updateAdGroupState('England Winning', 123456789101, adGrpStates.EnglandWinning);
      updateAdGroupState('England Losing', 123456789102, adGrpStates.EnglandLosing);
      updateAdGroupState('England Drawing', 123456789103, adGrpStates.EnglandDrawing);
    } else {
      Logger.log('No valid data found.');
    }
  } catch (e) {
    Logger.log('Error fetching data: ' + e.message);
  }
}
