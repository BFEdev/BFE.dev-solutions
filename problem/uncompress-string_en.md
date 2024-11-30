function uncompress(str) {
  let curNum=0, curStr='',stack=[]
  for(let i=0;i<str.length;++i){
    // If it is a digit
    if(!isNaN(parseInt(str[i]))){
      if(i>0 && !isNaN(parseInt(str[i-1]))){
        curNum = curNum*10+parseInt(str[i])
      } else {
        curNum=parseInt(str[i])
      }
    }
    //If it is opening brancket
    else if(str[i]==='('){
      stack.push({curNum,curStr});
      curNum=0;
      curStr=''
    }
    //If it is closing brancket
    else if(str[i]===')'){
      let element = stack.pop();
      curStr = element.curStr + curStr.repeat(element.curNum)
    }
    //If it is alphabets
    else {
      curStr+=str[i]
    }
  }
  return curStr
}
