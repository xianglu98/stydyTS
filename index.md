# 枚举 enum
```
enum CardSuit {
  Clubs,
  Diamonds,
  Hearts,
  Spades
}
enum EvidenceTypeEnum {
  UNKNOWN = "as",
  PASSPORT_VISA = "passport_visa",
  PASSPORT = "passport",
  SIGHTED_STUDENT_CARD = "sighted_tertiary_edu_id",
  SIGHTED_KEYPASS_CARD = "sighted_keypass_card",
  SIGHTED_PROOF_OF_AGE_CARD = "sighted_proof_of_age_card"
}

// 数字枚举   可以反向映射
console.log(CardSuit); // {0: "Clubs", 1: "Diamonds", 2: "Hearts", 3: "Spades", Clubs: 0…}
console.log(CardSuit[0]); // Clubs
console.log(CardSuit.Clubs); // 0
console.log(CardSuit[CardSuit.Spades]); //  Spades

//  字符串枚举   不可以反向映射
console.log(EvidenceTypeEnum); // {UNKNOWN: "as", PASSPORT_VISA: "passport_visa", PASSPORT: "passport", SIGHTED_STUDENT_CARD: "sighted_tertiary_edu_id", SIGHTED_KEYPASS_CARD: "sighted_keypass_card"…}
console.log(EvidenceTypeEnum.PASSPORT_VISA); //  passport_visa
console.log(EvidenceTypeEnum.as); // undefined error:不存在此属性
```
