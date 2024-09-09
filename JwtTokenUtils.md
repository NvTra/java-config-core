package com.tranv.d7shop.components;

import com.tranv.d7shop.exceptions.InvalidParamException;
import com.tranv.d7shop.models.User;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.io.Decoders;
import io.jsonwebtoken.io.Encoders;
import io.jsonwebtoken.security.Keys;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Component;

import java.security.Key;
import java.security.SecureRandom;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.function.Function;

//jwt.expiration=2592000
//jwt.secretKey="M9NQ+cvYJfYcoEBtYn7Hkjy0ofJ2kwrDbkdTu/v+bg8="

@Component
@RequiredArgsConstructor
public class JwtTokenUtils {
    @Value("${jwt.expiration}")
    private int expiration;

    @Value("${jwt.secretKey}")
    private String secretKey;


// Tạo token dựa trên thông tin của người dùng
    public String generateToken(User user) {
        //properties -> claims
		// Tạo map chứa thông tin claims (các thuộc tính của token)
        Map<String, Object> claims = new HashMap<>();
//                this.generateSecretKey();
// Đưa số điện thoại của người dùng vào claims
        claims.put("phoneNumber", user.getPhoneNumber());
		// Xây dựng và ký token với thuật toán HS256
        try {
            String token = Jwts.builder()
                    .setClaims(claims)// Thêm claims vào token
                    .setSubject(user.getPhoneNumber()) // Thiết lập subject là số điện thoại
                    .setExpiration(new Date(System.currentTimeMillis() + expiration + 1000L)) // Thiết lập thời gian hết hạn
                    .signWith(getSignKey(), SignatureAlgorithm.HS256)// Ký token bằng HS256 (phải chuyển secrecKey sang HS256)
                    .compact(); // Tạo token hoàn chỉnh
            return token;
        } catch (Exception e) {
            throw new InvalidParamException(e.getMessage());
        }
    }

    // // Tạo secretKey ngẫu nhiên (sử dụng trong trường hợp không có secretKey cố định)
    private String generateSecretKey() {
        SecureRandom random = new SecureRandom();
        byte[] keyBytes = new byte[32]; // 256-bit key
        random.nextBytes(keyBytes);
        String secretKey = Encoders.BASE64.encode(keyBytes);
        System.out.println(secretKey);
        return secretKey;
    }
 // Lấy key dùng để ký token
// Giải mã secretKey từ Base64
    private Key getSignKey() {
        byte[] bytes = Decoders.BASE64.decode(secretKey);
//        Decoders.BASE64.decode(secretKey)
        return Keys.hmacShaKeyFor(bytes); // Tạo key sử dụng thuật toán HMAC SHA256
    }

// Trích xuất toàn bộ claims từ token
    private Claims extractAllClams(String token) {
        return Jwts.parser()
                .setSigningKey(getSignKey()) // Thiết lập key ký
                .build()
                .parseClaimsJws(token) // Phân tích và xác thực token
                .getBody(); // Lấy phần claims của token
    }

// Trích xuất một phần cụ thể của claims từ token
    public <T> T extractClams(String token, Function<Claims, T> claimsResolver) {
        final Claims claims = this.extractAllClams(token); // Lấy toàn bộ claims
        return claimsResolver.apply(claims); // Áp dụng hàm để trích xuất giá trị cần lấy
    }

     // Kiểm tra xem token đã hết hạn hay chưa
    public boolean isTokenExpiration(String token) {
        Date expirationDate = this.extractClams(token, Claims::getExpiration);
        return expirationDate.before(new Date());
    }

// Trích xuất số điện thoại từ token
    public String extractPhoneNumber(String token) {
        return extractClams(token, Claims::getSubject);
    }

    public boolean validateToken(String token, UserDetails userDetails) {
        String phoneNumber = extractPhoneNumber(token);
        return (phoneNumber.equals(userDetails.getUsername())) && !isTokenExpiration(token);
    }
}
